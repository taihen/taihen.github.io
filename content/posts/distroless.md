---
title: "Distroless Containers"
date: 2025-06-10T20:42:01+01:00
draft: false
tags:
  [
    "automation",
    "infrastructure",
    "cloud",
    "IaC",
    "DevOps",
    "containers",
    "docker",
    "kubernetes",
    "distroless"
  ]
lastmod: 2025-06-10T20:42:01+01:00
---

If you’ve been in the container game for a while, you’ve probably seen a lot of buzz around “distroless” containers. The first time I heard the term, **I pictured a container floating off into the void - no OS, no shell, just… code**. Turns out, that’s not too far from the truth, but just like Serverless, Distroless is a misleading term!

Let’s break down what distroless containers are, why you might want them in your stack, what they’re great at (and not-so-great at), plus how to actually debug one.

## So, What’s a Distroless Container?

Distroless containers are container images that contain only your application and its runtime dependencies - **no shell, no package manager, no /bin/bash, nothing extra**.

Imagine your regular container, but someone Marie Kondo’d every single binary out except the ones your app actually needs.

Instead of starting with Ubuntu, Debian, or Alpine, you start with... nothing. Or, more specifically, with a minimal image built to only run your app

## Why Should I Care?

Pros:

- Security:
Smaller attack surface. If an attacker gets in, there’s no shell to exploit, no package manager to escalate with.
- Tiny Images:
Fast pulls, less storage, less network.
- Predictable Deployments:
“Works on my machine” issues go down deeper - if it’s not in the image, it’s not there, period.
- Audit-Friendly:
You know exactly what’s in your container (and what isn’t).

Cons:

- Debugging Is... Fun:
No shell or tools inside. Forget about `docker exec -it <container> sh`.
- You Need to Know Dependencies:
Miss one, and your app just won’t start.
- Not for All Apps:
If your app needs to run scripts or expects OS tools, distroless isn’t a good fit (at least not out-of-the-box).

## Prime Examples: Distroless Distributions

- Google’s Distroless Images:
The OG and probably most popular. There are also runtime flavors for Java, Go, Node.js, Python, and more. Built to be drop-in for language-specific apps.
- Chainguard Wolfi:
A “secure-by-default” Linux OS designed for containers, but still more minimal than Alpine or Debian, maintained.
- Scratch:
The literal empty base image in Docker - just the bare minimum to boot an app (Heads up, with scratch, you bring your own everything).

Each of these aims to cut out the cruft, shrink your images, and lower the risk surface.

### How Are These Distroless Images Built?

#### Google Distroless: The Bazel Way

Google’s Distroless project is open source and uses Bazel for building. Bazel lets them define exactly what goes into each image, with strict dependency control.

What’s Actually in a Google Distroless Image?

- No shell
- No package manager
- Only the minimal system libraries or language runtime (e.g., OpenJDK for Java, libc for base, nothing for static)
- Sometimes CA certificates for HTTPS

How They Build:

- Use Bazel build system
- Define everything in Bazel rules (BUILD and .bzl files)
- Explicitly list what files and libraries get included (or not included)
- Pull trusted Debian packages, then extract only required files (not whole packages)

For example, peek at how the distroless/static-debian12 image is defined:

```python
load("@distroless//package_manager:dpkg.bzl", "dpkg_package")

dpkg_package(
    name = "ca-certificates",
    package = "ca-certificates",
    version = "20240203",
)

container_image(
    name = "static-debian12",
    base = None,
    files = [
        ":ca-certificates",
    ],
    entrypoint = [],
    ...
)
```

- Bazel downloads the exact .deb files from Debian mirrors.
- It extracts just the files needed (like certs) and puts them in the image.
- There’s no /bin/sh, /usr/bin/apt, or any of that.
- The root filesystem is then exported as a minimal OCI/Docker image.

For more, check their [official repo](https://github.com/GoogleContainerTools/distroless) and [build rules](https://github.com/GoogleContainerTools/distroless/blob/main/BUILD.bazel).

#### Chainguard: The apko/Wolfi Way

Chainguard has their own [Wolfi Linux](https://github.com/wolfi-dev/os) as the build foundation, but for distroless/static images, they also strip out everything non-essential.

How They Build:

- Use [apko](https://github.com/chainguard-dev/apko), their declarative OCI image builder
- Define image contents in YAML (apko.yaml)
- Pull only specific, minimal Wolfi packages
- Build in a way that produces a provenance (SBOM) and signature for supply chain security

For example, chainguard/static image build [check their static image definition](https://github.com/chainguard-images/images/blob/main/images/static/apko.yaml):

```yaml
contents:
  packages:
    - ca-certificates-bundle
    - wolfi-baselayout

entrypoint:
  command: []
work-dir: /
accounts:
  users:
    - name: nonroot
      uid: 65532
      gid: 65532
      home: /home/nonroot
      shell: /sbin/nologin
```

- apko reads this YAML, grabs just those packages, and makes an image with nothing else.
- No shell, no busybox, no package manager.
- All images are signed and come with SBOMs for auditing.

Their images are automatically rebuilt and updated when upstream packages change, keeping things super fresh and secure.

Read more and see the full [apko static definition here](https://github.com/chainguard-images/images/blob/main/images/static/apko.yaml).

### Example `Dockerfile`

This step is not that different than any other container build that you've probably already seen - just a multistage `Dockerfile`.

Suppose you have this simple Go HTTP server:

```go
package main

import (
    "fmt"
    "net/http"
    "os"
)

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello, world! From Go %s\n", os.Getenv("GOVERSION"))
    })
    http.ListenAndServe(":8080", nil)
}
```

#### With Google Distroless

Google’s Distroless images are basically the gold standard. For Go, they recommend the `gcr.io/distroless/static` or, for apps that need glibc, `gcr.io/distroless/base`.

```docker
# Build Stage
FROM golang:1.24 as builder

WORKDIR /app
COPY . .
ENV CGO_ENABLED=0
RUN go build -o app .

# Distroless Stage
FROM gcr.io/distroless/static

COPY --from=builder /app/app /app
ENV GOVERSION=1.24
USER nonroot:nonroot
ENTRYPOINT ["/app"]
```

How it works:

- Use Go 1.24 for building.
- Set `CGO_ENABLED=0` to produce a static binary (no libc/glibc dependencies).
- Use `gcr.io/distroless/static` as the runtime image. This image contains only minimal libraries (not even a shell).
- Set an env variable for demo.
- Runs as a non-privileged user.

#### With Chainguard Distroless

Chainguard Images are another great, secure, minimal alternative. They’re signed, SBOM-enabled, and often more up-to-date than Google’s due to rolling updated. (Wolfi is rolling Linux distribution, so similar philosophy to Arch or Gentoo)

Chainguard has images for Go (chainguard.dev Go base), as well as a general-purpose distroless image called chainguard/static.

```docker
# Build Stage
FROM cgr.dev/chainguard/go:1.24 as builder

WORKDIR /app
COPY . .
ENV CGO_ENABLED=0
RUN go build -o app .

# Distroless Stage
FROM cgr.dev/chainguard/static

COPY --from=builder /app/app /app
ENV GOVERSION=1.24
USER 65532:65532
ENTRYPOINT ["/app"]
```

How it works:

- Uses `cgr.dev/chainguard/go:1.24` for building.
- Produces a static binary with Go 1.24.
- Uses `cgr.dev/chainguard/static` as the distroless image.
- Runs as a non-privileged user.

## Debugging Distroless Containers: The Real-World Way

Okay, so you built your shiny, ultra-minimal distroless container. You deploy…
And something breaks. Uh-oh, what now?

Here’s the problem: there’s no shell, no way to `docker exec -it <container> /bin/bash` in.

How do you debug then?

### Ephemeral Debug Containers (Kubernetes)

If you’re on Kubernetes (at least 1.25) you can add ephemeral containers to a running Pod:

```sh
kubectl debug -it <your-pod> --image=<debug-container> --target=<your-container>
```

This spins up a debug container, then in the same pod you have a shell and all required tools.

### Temporary Debuggable Images

If you’re troubleshooting locally, swap the base image for something with a shell (like Debian):

```sh
FROM debian:bookworm-slim
COPY --from=builder /app/app /app
ENTRYPOINT ["/app"]
```

Now you can `docker exec -it <container> /bin/bash`.

> It is common practice to build at least two types of base container images:
>
> - latest:
> A minimal production ready base image.
> - debug:
> An image that contains also a shell and/or any other tooling which you might need for application development and debugging purposes,
>
> This approach simplifies development while providing the same building block (instead of using ie. completely different image with different underlying Linux distribution).

### Enhanced Logging

Because you can’t poke around inside, make your Go app extra chatty:

- Print config, env, and version on startup
- Add /debug/pprof endpoints for introspection
- Log important lifecycle events

### Chroot Debug (local Docker)

```sh
docker run --rm -it --pid=container:<your-container-id> --network=container:<your-container-id> -v /:/host busybox chroot /host
```

This gives you a shell on your container’s filesystem—handy for the brave.

## Why All This Matters

Both Google and Chainguard go to extreme lengths to:

- Include only what’s truly needed
- Build from trusted, minimal upstream sources (Debian/Wolfi)
- Ensure everything is open, reproducible, and auditable
- Make images signed and easy to verify (especially Chainguard)

**The end result?**

**Production containers that are tiny, secure, and predictable.**

## I Build My Own Images!

You don't have to use any the public Distroless base images, you can just build your own one! There are many legitimate reason to do so.

1. Precise Control Over What’s Included

- Only what you need, nothing more:
You decide exactly which files, libraries, and tools are present.
- Custom dependencies:
Official distroless images are generic; maybe you need a specific shared library, locale, or custom CA bundle.

2. Security & Compliance

- Meet your dreadful organization’s security policies:
Need to audit every file? Require custom hardening or only trusted sources?
- Provenance:
You know the origin of every package and file, which is crucial for regulated environments or when you must comply with frameworks like SLSA, CIS, or PCI-DSS.

3. Supply Chain Transparency

- You own your SBOM:
Generating your own Software Bill of Materials means you (not a third party) can guarantee what’s in your container.
- Reproducible builds:
You can rebuild your images whenever dependencies are patched (think OpenSSL zero-day), and not wait for the upstream base to update.

4. Customization & Optimization

- Performance tuning:
You might want to optimize for startup speed or size by stripping even more than the public images.
- Add your own users, entrypoints, or app conventions:
For instance, set a default non-root user that fits your internal UID/GID schema.
- Multi-arch support:
Official images sometimes lag behind on ARM64, RISC-V, etc.

5. Isolation for Special Use Cases

- Non-standard languages/runtimes:
If you use a custom or less-common language/runtime, there may not be an official distroless for it.
- Legacy app support:
Sometimes your dependencies need an older or patched system library version, not what’s in the upstream distroless.

6. Faster Patching & Release Cadence

- Control over updates:
Security fixes, new versions, and bug patches can be applied on your schedule, not someone else’s.

7. Educational Value & Internal Trust

- Better understanding:
Building your own distroless image is a great way to learn about Linux packaging, containers, and supply chain security.
- Internal trust:
Teams may trust internally-built images more than third-party ones, especially for critical infrastructure.

All the tools that both Google and Chainguard is using for their builds are Open Sourced and their projects are great examples of how to use them.

I've build my own Distroless super minimal image using apko and Wolfi - it is great example how you can build your own one:
- https://github.com/taihen/base-image

## Final Thoughts

Distroless containers are like carrying only what you absolutely need on a trip - no extra baggage, way less to lose, and much harder for a thief to rob you.

For production, they’re a win for security and efficiency.

Just don’t forget: debugging is a little trickier, but with some clever workarounds (and good logs!), you can be fearless - even with distroless!