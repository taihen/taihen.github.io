---
title: "Distroless Was the Easy Part"
date: 2026-07-14T15:30:00+02:00
lastmod: 2026-07-14T15:30:00+02:00
draft: false
tags:
  - containers
  - docker
  - kubernetes
  - distroless
  - security
  - supply-chain
  - sbom
  - slsa
  - automation
  - DevOps
---

Last year I wrote about [distroless containers](/posts/distroless/) and finished with a small brag: I build my own minimal base image with [apko](https://github.com/chainguard-dev/apko) and [Wolfi](https://github.com/wolfi-dev/os), look how tiny it is, look how few packages it has.

This year I held that image against the standards that serious hardened-image vendors compete on. **It failed.**

Not because of what was inside the image. The contents were fine. Six packages, no shell, non-root user, signed with [Cosign](https://github.com/sigstore/cosign). It failed because of everything around the image and that is exactly where the state of the art has moved.

## The Bar Has Moved

In 2025 the question was "how little can you ship?". Distroless answered it: ship your app, its runtime dependencies, and nothing else.

In 2026 the question is different: **"can you prove it?"**

Can a cluster, at the moment it admits your pod, verify:

- who built this image, from which commit, on which builder?
- what exactly is inside it, from a source it can trust more than your README?
- that it was scanned and that nothing consumable ever pointed at an unscanned build?

Minimal contents are table stakes now. The competition between [Chainguard](https://www.chainguard.dev/), [Docker Hardened Images](https://www.docker.com/products/hardened-images/) and the rest of the hardened-image crowd is not about who has fewer packages, because they all have almost none. It is about the evidence attached to the image and whether machines can consume that evidence without a human in the loop.

There is a phrase worth stealing here: **admission-consumable**. An SBOM in a tarball on your release page is documentation. An SBOM attached to the image in the registry, signed and discoverable by digest, is evidence. Only one of them can stop a bad deploy.

## My Image Looked Great and Proved Nothing

Here is what my [base-image](https://github.com/taihen/base-image) pipeline did until this week. See if any of it looks familiar:

- apko generated a beautiful [SPDX](https://spdx.dev/) SBOM on every build... which got uploaded as a GitHub Actions artifact and attached to the release page. Nothing in the registry. A scanner or admission controller looking at the image itself would find no SBOM at all.
- There was no build provenance. The image was signed, so you knew *I* pushed it, but nothing attested *how* it was built or from what commit.
- And my favorite: the workflow published straight to `:latest`, signed it, and *then* ran the vulnerability scan in a later job. **For a window on every single build, `:latest` pointed at a signed, unscanned image.** If [Trivy](https://github.com/aquasecurity/trivy) found something critical, the tag had already moved.

Oh, and the security scan itself ran via `aquasecurity/trivy-action@master`. A mutable tag. In a supply chain security repo. Physician, heal thyself.

None of this showed up by looking at the image. The image was perfect. The *process* leaked.

So I rebuilt the pipeline. What follows is each feature I added, where the idea comes from, and how to verify it yourself. Evidence you cannot check is just marketing.

## 1. Publish First, Promote Later

The oldest idea in the list, and the one registries made everyone forget. The Maven and Artifactory crowd have preached "build once, promote the same artifact through stages" for two decades, but pushing a container straight to a consumable tag is so convenient that the lesson got lost. A tag is a pointer, moving a pointer is a release decision, and a release decision deserves a gate.

My build job now publishes each variant under an ephemeral, run-scoped tag that no human or cluster would ever pull:

```yaml
EPHEMERAL_PREFIX="$OCI_HOST/$OCI_REPO:build-${{ github.run_id }}"

base_digest=$(apko publish base.yaml "$EPHEMERAL_PREFIX-base" \
  --lockfile base.lock.json \
  --sbom-path=sbom-base)
```

Tests and the Trivy scan run against those digests. Only after they pass does a separate `promote` job sign the image, attach the attestations and move the floating tags:

```yaml
promote:
  if: github.event_name == 'push' || github.event_name == 'schedule'
  needs: [build, test]
```

A bad build can still happen. It just cannot be *reached* anymore, not through `:latest` and not through anything you would put in a `FROM` line. Signing moved behind the scan too, so a signature now means "built by CI *and* passed the gate" rather than just "built by CI".

To convince yourself a pipeline actually works this way, poke the registry with [`crane`](https://github.com/google/go-containerregistry) (from the go-containerregistry project). `crane ls ghcr.io/taihen/base-image` lists every tag including the ephemeral build ones, and `crane digest ghcr.io/taihen/base-image:latest` tells you what the consumable tag points to. The real check is cross-referencing: that digest should appear in a CI run *after* its test job, never before, which the [GitHub CLI](https://cli.github.com/) will show with `gh run view --log`. If a pipeline cannot answer "which run promoted this digest?", that is the finding.

## 2. Keyless Signatures

Every promoted image is signed with Cosign in keyless mode, which means no private key exists to leak. The signature is bound to my CI workflow's OIDC identity, certified for about ten minutes by a public CA and logged forever in a transparency log.

The machinery behind that is [Sigstore](https://www.sigstore.dev/), a Linux Foundation project started in 2021. [Fulcio](https://github.com/sigstore/fulcio) issues short-lived certificates against OIDC identities, [Rekor](https://github.com/sigstore/rekor) keeps an append-only transparency log, Cosign is the client. The design lift comes straight from Certificate Transparency and Let's Encrypt: replace long-lived secrets with short-lived certificates plus a public log. It killed the worst part of image signing, which was the key management nobody actually did.

When verifying, always pin the *identity*, because "a valid signature exists" means nothing when anyone can sign anything with their own identity:

```sh
cosign verify ghcr.io/taihen/base-image:latest \
  --certificate-identity=https://github.com/taihen/base-image/.github/workflows/build.yml@refs/heads/main \
  --certificate-oidc-issuer=https://token.actions.githubusercontent.com
```

Two more tools worth knowing. `cosign tree <image>` shows everything hanging off an image in the registry (signatures, attestations, SBOMs) in one view, and `rekor-cli search --sha <digest>` finds the transparency log entries, which is how you would notice someone signing images with your identity at 3 AM.

## 3. SBOM as an Attestation, Not a Download

The SPDX SBOM that apko generates during the build is now attached to the image in the registry as a signed attestation:

```sh
cosign attest --yes --type spdxjson \
  --predicate sbom-output/base_sbom-index.spdx.json \
  "$digest"
```

Same SBOM as before, completely different value. Before, it was a file humans could download if they knew where to look. Now any tool that has the image digest can fetch it, verify who signed it and make a decision. No GitHub account, no release page, no trust in my documentation required.

SPDX itself started around 2010 at the Linux Foundation as a *license* compliance format, with security as an afterthought (its OWASP-born sibling is [CycloneDX](https://cyclonedx.org/)). What yanked SBOMs from compliance backwater to mainstream requirement was SolarWinds. The US Executive Order 14028 in May 2021 told federal software suppliers to provide SBOMs, NTIA published the "minimum elements", and suddenly every serious registry and scanner grew SBOM support. The attestation wrapper is [in-toto](https://in-toto.io/), the framework that standardizes "a signed statement about an artifact".

Checking it has three levels, increasingly paranoid:

```sh
# 1. it exists and the signature checks out
cosign verify-attestation "$IMAGE" --type spdxjson \
  --certificate-identity="$IDENTITY" --certificate-oidc-issuer="$ISSUER"

# 2. read what it actually claims
cosign verify-attestation "$IMAGE" --type spdxjson ... \
  | jq -r '.payload' | base64 -d | jq '.predicate.packages[].name'

# 3. don't trust it at all: generate your own and compare
syft "$IMAGE" -o spdx-json > independent-sbom.json
```

That third one is the important habit. [`syft`](https://github.com/anchore/syft) (from Anchore) builds an SBOM from the image contents directly, and if the attested SBOM and the independent one disagree on packages, someone is lying. Remember that SBOMs are input, not output. Feed them to [`grype`](https://github.com/anchore/grype) with `grype sbom:./independent-sbom.json` or to Trivy with `trivy sbom ./independent-sbom.json` and you get a vulnerability verdict from the SBOM alone, no image pull needed.

## 4. SLSA Provenance

A signature says *who*. Provenance says *how and from what*: a signed statement attached to the image declaring it was built by this workflow, in this repo, at this commit, triggered by this event. If someone pushes an image to my registry with a stolen token, it will carry no provenance, or provenance naming the wrong builder, and a verifier catches it.

The framework is [SLSA](https://slsa.dev/), "Supply-chain Levels for Software Artifacts", which is Google's internal Binary Authorization for Borg open-sourced through OpenSSF in 2021. The core idea from two decades of Google prod: **an artifact is trustworthy because its build process is attested, not because of where it sits.** The predicate format is in-toto again. What took SLSA from conference-talk material to something you would actually deploy was GitHub shipping [artifact attestations](https://docs.github.com/en/actions/security-for-github-actions/using-artifact-attestations) in 2024, which reduced it to this:

```yaml
- uses: actions/attest-build-provenance@0f67c3f4 # v4.1.1
  with:
    subject-name: ghcr.io/${{ github.repository }}
    subject-digest: ${{ steps.digests.outputs.base-sha }}
    push-to-registry: true
```

Years of supply chain talk, four lines of YAML. Take the win.

Verification asks the question that matters, "was this really built by that repo's workflow?":

```sh
gh attestation verify oci://ghcr.io/taihen/base-image:latest --repo taihen/base-image

# read the claims: builder, commit, trigger
gh attestation verify oci://$IMAGE --repo taihen/base-image --format json \
  | jq '.[].verificationResult.statement.predicate.buildDefinition'
```

Cross-check the commit SHA in the predicate against the git tag you think you are running. For provenance from the slsa-github-generator ecosystem there is also [`slsa-verifier`](https://github.com/slsa-framework/slsa-verifier), while for GitHub artifact attestations `gh` is the native tool and recent Cosign versions can verify the bundles too.

## 5. Reproducible Builds

My old pipeline rebuilt daily and floated with whatever Wolfi had that morning. Fresh, yes. But "what exactly was in Tuesday's image?" had no reliable answer, and rebuilding the same commit twice gave two different images.

Two changes fixed that. Package versions are now pinned in committed lockfiles, an idea lifted from the language package managers (`Gemfile.lock`, `package-lock.json`, `go.sum`) and applied to OS packages:

```sh
apko lock base.yaml --output base.lock.json
```

And image timestamps come from the commit date instead of the wall clock:

```sh
SOURCE_DATE_EPOCH=$(git log -1 --format=%ct)
```

`SOURCE_DATE_EPOCH` is literally a [reproducible-builds.org](https://reproducible-builds.org/) spec, born in the Debian project around 2015 from people who spent a decade making an entire distribution build bit-for-bit identically. Their argument is the strongest in this whole post: **if two people cannot build the same source into the same binary, you cannot prove the binary came from the source.** Everything else here attests the build. Reproducibility lets you *re-run* it.

Same commit in, same image out, byte for byte. Freshness did not die either. A daily job regenerates the lockfiles and opens a PR when Wolfi moves, so every package bump is a reviewable diff instead of silent drift.

One trap worth knowing: the lazy route to a reproducible timestamp is epoch zero, and some tooling defaults to exactly that. Then your images claim to be built in 1970 and every freshness check in the world flags them. Commit date gives you determinism *and* a timestamp that means something.

This is the one claim you verify by doing rather than by reading:

```sh
git checkout <commit>
apko build base.yaml test:local img.tar --lockfile base.lock.json --sbom-path /tmp/s
# compare your digest against the published one
crane digest ghcr.io/taihen/base-image:latest
```

Same digest: proven. Different digest: [`diffoscope`](https://diffoscope.org/) (also from the reproducible-builds project) run as `diffoscope img.tar published.tar` shows exactly which bytes differ and usually embarrasses a timestamp somewhere.

## 6. Pin the Pipeline Itself

The `@master` confession above got fixed the boring way, with every action pinned to a commit SHA:

```yaml
uses: aquasecurity/trivy-action@ed142fd0673e97e23eac54620cfb913e5ce36c25 # v0.36.0
```

Your build pipeline is part of your supply chain. An attacker who can move a tag on an action you run has code execution in the job that signs your images. Tags are mutable, SHAs are not, and [Dependabot](https://github.com/dependabot) still bumps pinned SHAs, so the maintenance cost stays a PR review.

This lesson is scar tissue rather than theory. The Codecov bash uploader compromise in 2021 taught everyone that CI tooling fetched by mutable reference is an injection point. Then `tj-actions/changed-files` in March 2025 made it specific to Actions: the attacker rewrote existing version tags to point at malicious commits, every workflow pinned to a *tag* ran secret-dumping code, and workflows pinned to a *SHA* were untouched. Hard to ask for a cleaner controlled experiment.

The audit tooling matured a lot here:

```sh
# static audit of workflow security, unpinned actions included
zizmor .github/workflows/

# OpenSSF Scorecard has a dedicated Pinned-Dependencies check
scorecard --repo=github.com/taihen/base-image --checks=Pinned-Dependencies
```

[`zizmor`](https://github.com/zizmorcore/zizmor) catches a lot more than pinning (template injection, credential leaks into artifacts), [OpenSSF Scorecard](https://github.com/ossf/scorecard) grades your whole repo including that check, and [`pinact`](https://github.com/suzuki-shunsuke/pinact) or [`frizbee`](https://github.com/stacklok/frizbee) will rewrite tag references to SHAs for you, so there is no excuse about the tedium.

## 7. Fail Closed

Small one, but it bit me conceptually. My release gating compared SBOMs between builds to decide "did anything change?". If the current SBOM was missing or empty, say a build bug produced no SBOM at all, the comparison happily concluded "no change" and moved on.

That is failing open. A defective build read as a quiet day. The script now treats a missing or empty SBOM as a build failure, full stop. The principle comes from safety engineering, decades before software: a failed component must leave the system in its safe state, dead man's switch rather than stuck accelerator. In pipelines the failure mode is subtler because "absence of signal" and "signal of absence" look identical in glue code, and glue code is where release decisions actually live.

There is no tool for this one. You check it by testing the failure path, not the happy path: delete the SBOM in a scratch branch and confirm the build goes red. If you have never watched your gate fail, you do not have a gate, you have a decoration.

## Making the Cluster Care

All of this evidence exists so that a machine can act on it. On Kubernetes, Sigstore's [`policy-controller`](https://github.com/sigstore/policy-controller) or [Kyverno's](https://kyverno.io/) `verifyImages` rules can require, at admission time, a valid signature from your CI identity, an SBOM attestation and provenance from the expected builder. A pod without them never schedules.

That is the payoff of "admission-consumable". The evidence is not for your auditor's PDF, it is for the machine that decides whether your workload runs.

## The Honest Frontier: VEX

There is one piece of the state of the art I deliberately have not implemented: VEX, the Vulnerability Exploitability eXchange. It is the mechanism for saying, in a signed and machine-readable way, "yes, CVE-XXXX exists in this package, and no, this image is not affected, and here is why".

It came out of the same post-SolarWinds NTIA and CISA working groups as the SBOM push, once everyone realized SBOMs created a new problem: scanners now *see* everything, including the 38 findings that do not matter. The open implementation is [OpenVEX](https://github.com/openvex), with [`vexctl`](https://github.com/openvex/vexctl) for authoring and consumers like `trivy --vex` and Grype honoring the statements. It is the difference between your scanner screaming about 40 findings and showing the 2 that matter.

I skipped it, on purpose. Everything else in this post is a one-time pipeline change. VEX is an editorial commitment where every statement is a human triage verdict that has to be re-validated as package versions move. A stale `not_affected` is worse than no statement at all, because it actively suppresses a real finding in every downstream scanner that trusts you. For a hobby base image with six packages and a maintainer count of one, honest silence beats confident staleness.

There is no free option here, only a choice of who does the work. Either you maintain the triage verdicts yourself, forever, or you pay a provider like Chainguard to do it for you and that is precisely where their money is made. You are not paying for small images. You are paying for someone whose full-time job is keeping those verdicts true.

## The Checklist

If you build your own images, and [I still think you should](/posts/distroless/) if only to learn, here is the bar as of 2026:

- [ ] Nothing consumable (no `:latest`, no version tags) points at an unscanned image, ever
- [ ] Signature: keyless Cosign, verified against your CI identity, not just "signed by someone"
- [ ] SBOM: attached to the image as a signed attestation, cross-checkable with an independent `syft` scan
- [ ] Provenance: SLSA attestation binding image to commit, workflow and builder
- [ ] Reproducible: locked package versions, timestamps from the commit, rebuild gives the same digest
- [ ] Pipeline pinned: every action by SHA, audited with `zizmor` or Scorecard
- [ ] Gating fails closed: missing evidence is a build failure, and you have watched it fail on purpose
- [ ] VEX: only if you will actually maintain it

And the toolbox in one place: `cosign` and `rekor-cli` for signatures, `crane` for registry archaeology, `syft`, `grype` and `trivy` for SBOMs and scanning, `gh attestation` and `slsa-verifier` for provenance, `diffoscope` for reproducibility, `zizmor`, `scorecard` and `pinact` for the pipeline itself, `vexctl` when you are ready for VEX, and `policy-controller` or Kyverno to make the cluster enforce all of it.

The full implementation is one PR if you want to see every diff: [taihen/base-image#20](https://github.com/taihen/base-image/pull/20).

## What Nobody Solves Yet

Everything above is buyable or buildable today. It would be dishonest to end there, because the more evidence I attached to this image, the more obvious it became where the evidence stops.

Start at the bottom of the pyramid. Every attestation chain in this post ends at a git commit, and nothing whatsoever vouches for the commit itself. Provenance proves my image was built from commit X and stays silent on whether commit X deserved to exist. The xz-utils backdoor is the brutal counterexample: flawless provenance, signed releases, a legitimate maintainer identity, and the malicious code *was* the source. There is no `cosign verify-review`, no machine-checkable artifact saying two independent humans looked at this change, no attestation of maintainer identity continuity. We built a magnificent tower of proof on an unattested foundation. And it is about to get worse, because an ever-growing share of commits is written by AI agents and a signed commit says nothing about what produced the code. When the reviewer is also an agent, the one attestation we never built becomes the one we need most.

Then there is the strange fact that we industrialized producing evidence and never showed up to read it. Everybody writes to Rekor, the transparency log, and almost nobody monitors it. Certificate Transparency became useful the day people started watching the logs and alerting on misissued certificates. The Sigstore equivalent barely exists, which matters because keyless signing moved the target from "steal a key" to "compromise the workflow", and a workflow-injection attacker produces signatures that verify perfectly. Nothing production-grade will tell you that a signature appeared under your CI identity at 3 AM that you never made. Reproducibility has the same consumption gap. I made my builds reproducible in section 5, and who exactly is reproducing them? Debian has independent rebuilders cross-checking the archive bit for bit. Containers have no rebuild network at all, so "reproducible" still means "trust my single builder, but politely".

Time is the next hole. Admission control verifies evidence at the moment a pod is created, and evidence does not hold still. A CVE gets published, a VEX verdict flips to affected, an identity turns out to have been compromised since March. The photograph was accurate when taken and the movie kept playing. No closed loop exists from "the evidence changed" to "re-evaluate the running fleet and drain what no longer passes". Revocation in general is the weakest verb in this whole vocabulary: try expressing "distrust everything this identity signed between those two dates" in a way any admission controller will actually enforce.

Composition is quietly unsolved too. Policy engines verify one image at a time, but my app image builds on my base image, which apko assembled from Wolfi packages, which [melange](https://github.com/chainguard-dev/melange) built from upstream tarballs. Each hop may carry beautiful attestations and no tool walks the chain as a chain. The bitter irony is that this was in-toto's original vision, whole-pipeline layouts, and the ecosystem adopted its envelope format while letting the actual idea die. Meanwhile the SBOM sitting at every link of that chain is a claim wearing a signature: two generators pointed at the same image routinely disagree, statically-linked and vendored code silently vanishes, and nothing attests that every byte in the image is accounted for. Until completeness is a measurable number, "we have an SBOM" tells you less than everyone pretends.

And at the very top, the seam nobody has closed: all of this evidence describes the image at rest. Confidential computing can attest what a machine is executing right now, yet joining the two ends, this attested commit became this attested image now running in this attested enclave as one verifiable chain, is still research, not product. Even without enclaves, nothing standard ships "this image should only make these syscalls and talk to these endpoints" as a signed artifact next to the SBOM.

Notice the pattern. None of these are missing formats. Every one is a missing feedback loop, a place where evidence gets produced and never consumed, checked once and never rechecked, attested per piece and never as a whole. The last five years standardized the paperwork. The next five have to build the readers.

## Final Thoughts

Last year's post ended by saying distroless is like traveling light. Less baggage, less to steal. Still true. But packing light was never the hard part of security at the airport. The hard part is proving who you are, where you came from and that someone checked your bag.

The image is the easy part. **The proof is the product.**
