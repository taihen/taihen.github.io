---
title: "The Evolution of Configuration Management"
date: 2025-01-07T21:45:01+01:00
draft: false
tags:
  [
    "25years",
    "automation",
    "infrastructure",
    "cloud",
    "IaC",
    "DevOps",
    "history",
  ]
lastmod: 2025-06-11T10:38:36+01:00
---

Over my two decades as an infrastructure engineer, I've watched our field transform dramatically. What began with manual server configuration has evolved into defining entire organizations infrastructure as code. This journey reflects not just technological change, but a complete shift in how we approach managing systems at scale. I've been in the trenches through most of it, and I want to share my story of this evolution with fellow infrastructure engineers who've lived through similar transitions.

## The Early Days: Manual Configuration and Basic Tools

### ClusterSSH: When Copy-Paste Was "Automation"

When I started out in the early 2000s, "infrastructure management" meant logging into servers one by one. My first taste of "automation" came with [ClusterSSH](https://github.com/duncs/clusterssh) - a basic tool that let me run commands on multiple SSH sessions at once.

The concept was dead simple: I'd open an administration console and multiple terminal windows to my servers. Whatever I typed in the admin console would be copied to all connected servers. No versioning, no rollback capability, and a single typo could bring down your entire environment. But compared to connecting to each server individually, this felt like magic.

### CFEngine: The First Real Configuration Management

As server fleets grew beyond what ClusterSSH could handle, I discovered [CFEngine](https://cfengine.com/) - one of the pioneers of true configuration management. Created in 1993 by [Mark Burgess](https://markburgess.org/bio.html), CFEngine introduced something revolutionary: describing the desired state of your systems instead of writing step-by-step scripts.

With CFEngine, I could specify that a particular configuration file should exist with specific content and permissions, and CFEngine would make it happen regardless of the starting state. This approach addressed a growing problem - shell scripts were becoming unmanageable jumbles of conditionals to handle different OS quirks.

The learning curve was steep (CFEngine had its own DSL that felt alien), but it solved real problems of consistency. As noted in ["Configuration Management: Definition and Benefits"](https://www.atlassian.com/microservices/microservices-architecture/configuration-management) by Atlassian, this type of systems engineering process established consistency throughout a service life.

```shell
# filepath: /path/to/cfengine_example.cf
bundle agent example {
  files:
    "/etc/example.conf"
      comment => "Ensure example.conf exists with correct content",
      create => "true",
      perms => mog("644", "root", "root"),
      edit_line => insert_lines("example configuration content");
}
```

## The Rise of Modern Configuration Management

### Puppet and Chef: Industrial-Strength Solutions

By the late 2000s, I was managing thousands of servers, and two new tools had emerged: [Puppet](https://puppet.com/) and [Chef](https://www.chef.io/).

Puppet, created by [Luke Kanies](https://lukekanies.com/about/) in 2005, introduced a declarative domain-specific language that made it easier to describe system configurations. I'd define resources - packages to install, files to manage, services to run - and Puppet would figure out how to make it happen. The master-agent architecture meant I had a central server that stored configurations and pushed them out to agents on each node.

Chef took a different approach, using Ruby for its configurations instead of DSL. While this made Chef more powerful for complex scenarios (you had the full power of a programming language), it also meant a steeper learning curve. I found Chef valuable for complex deployments, but many team members not only often struggled with its Ruby-based approach.

Both tools significantly improved how I managed infrastructure, but they shared common challenges: complex setup, agent management headaches, and certificate overhead. Theoretically could almost arbitrary number of servers, although at early stages, both struggle with large fleets of servers due to master-agent architecture. Both had default implementations that could only reliably manage a limited number of nodes, requiring architectural modifications and performance optimizations to scale effectively. Running large infrastructures with either tool needed to implement load balancing, distributed architectures, and careful resource management to overcome these limitations - which made most of the implementations complicated.

```puppet
# filepath: /path/to/puppet_example.pp
file { '/etc/example.conf':
  ensure  => file,
  content => "example configuration content\n",
  owner   => 'root',
  group   => 'root',
  mode    => '0644',
}
```

```ruby
# filepath: /path/to/chef_example.rb
file '/etc/example.conf' do
  content 'example configuration content'
  owner 'root'
  group 'root'
  mode '0644'
  action :create
end
```

### SaltStack: Event-Driven Automation

Around the same time, I began experimenting with [SaltStack](https://saltproject.io/) (or simply "Salt"), which offered a different approach. Salt used YAML files called "states" to define configurations and ZeroMQ for communications between the master and minions (agents).

What I loved about Salt was its event-driven nature. Rather than just periodically applying configurations, Salt could react to events in real-time. When a new server came online, it would automatically get the right configuration without manual intervention.

For bare metal environments, I combined Salt with PXE boot servers and kickstart files to automate the entire provisioning process. A new physical server would boot via PXE, get its OS installed, then register with the Salt master and receive its configuration. This approach let me take a server from bare metal to production-ready with zero manual steps.

```yaml
# filepath: /path/to/salt_example.sls
/etc/example.conf:
  file.managed:
    - source: salt://example/example.conf
    - user: root
    - group: root
    - mode: 644
```

### Ansible: Simplicity Wins

In 2012, [Ansible](https://www.ansible.com/) emerged and quickly became my go-to tool and ditch my tool of choice at the time - Puppet. The key difference? Ansible _eliminated the need for agents_, instead just using SSH - a protocol already enabled on all servers.

The simplicity was a game-changer: no agents to install, no certificates to manage, and configurations written in YAML that anyone could understand.

The [Google SRE Workbook](https://sre.google/workbook/table-of-contents/) highlights this benefit, noting that "as the number of applications, servers, and variations increases over time, configuration can become very complex and verbose" - a problem that Ansible's straightforward approach helped to mitigate.

```yaml
# filepath: /path/to/ansible_example.yml
- name: Ensure example.conf exists
  hosts: all
  tasks:
    - name: Create example.conf
      copy:
        content: "example configuration content\n"
        dest: /etc/example.conf
        owner: root
        group: root
        mode: "0644"
```

#### Network Automation Revolution

One area where Ansible truly changed my life was network automation. Before Ansible, configuring network devices was a manual, error-prone process involving CLI commands on each device, and no Puppet nor Chef could address those types of challenges due to agent architecture.

Ansible brought infrastructure-as-code principles also to network management. Now I could define my network configurations in YAML playbooks, version control them, and apply them consistently across devices - same way as I would do that for servers. For someone who had spent countless hours clicking through terrible network management consoles or pasting CLI commands, this was revolutionary.

```yaml
- name: Configure Network Devices
  hosts: network_devices
  tasks:
    - name: Configure Interface
      ios_config:
        lines:
          - "interface FastEthernet0/0"
          - "ip address 192.168.0.1 255.255.255.0"
          - "no shutdown"
```

##### Event-Driven Automation with Ansible Automation Platform

Additionally, when RedHat acquired Ansible, they have developed [Ansible Automation Platform](https://www.redhat.com/en/technologies/management/ansible-automation-platform) as a solution for automating network processes across devices and domains. One of the powerful features of this platform is its ability to respond to events in real-time. For example, I could set up an automation that triggers when a network interface goes down on some Cisco IOS device.

```yaml
# filepath: /path/to/ansible_event_driven.yml
- name: Monitor and respond to interface status
    hosts: network_devices
    gather_facts: no
    tasks:
        - name: Check interface status
            ios_command:
                commands:
                    - show interfaces FastEthernet0/0
            register: interface_status

        - name: Trigger event if interface is down
            ansible.builtin.debug:
                msg: "Interface FastEthernet0/0 is down!"
            when: "'FastEthernet0/0 is down' in interface_status.stdout"
```

To integrate this with Ansible Automation Platform, I could setup automated remediation of the issue, such as reconfiguring the interface or notifying the on-call team.

```yaml
# filepath: /path/to/ansible_remediation.yml
- name: Remediate interface down event
    hosts: network_devices
    gather_facts: no
    tasks:
        - name: Reconfigure interface
            ios_config:
                lines:
                    - "interface FastEthernet0/0"
                    - "no shutdown"
            when: "'FastEthernet0/0 is down' in interface_status.stdout"
```

## Scaling Network Automation Beyond Ansible

### The Scalability Challenge

While Ansible worked brilliantly for most network automation tasks. Yet again, I've encountered challenges when scaling to very large environments with thousands of devices. The sequential execution model of Ansible could become a bottleneck, and complex workflows sometimes required more programming flexibility than YAML provides.

This led me to explore complementary tools that address these specific scaling challenges.

### Nornir: Python-Powered Network Automation

One tool that's been a game-changer for large-scale network automation is [Nornir](https://nornir.readthedocs.io/). Unlike Ansible, which uses YAML playbooks, Nornir is 100% Python. This means I can leverage the full power of Python for complex network automation tasks.

What makes Nornir particularly valuable for scaling is its parallel execution capability. Nornir can execute jobs in parallel across hundreds or thousands of devices, dramatically reducing the time required for network-wide changes. This makes it ideal for large-scale networks where sequential task execution would be prohibitively slow.

I've found that Nornir complements Ansible rather than replacing it. I used Ansible for most day-to-day network automation tasks and switch to Nornir when I need the scalability of parallel execution or when I need more complex logic than YAML can easily express.

### A Holistic Approach to Network Automation

Through my experience, I've realized that scaling network automation requires more than just good tools—it requires a holistic approach that integrates network automation with other IT systems and processes.

Many organizations struggled with scaling network automation because they focus on automating specific tasks on specific devices without considering the broader workflow. This piecemeal approach inevitably hits a wall when you need to orchestrate changes across different network domains and integrate with IT service management systems.

The key insight is that truly scalable network automation requires end-to-end orchestration across multiple network domains and IT systems. Tools like [Itential](https://www.itential.com/) have emerged to address this need, enabling teams to build automation that address specific use cases and then expand to include integration with IT systems for the entire process from ticket creation to closure.

## The Paradigm Shift: From Configuration Management to Infrastructure as Code

### The Fundamental Difference

As cloud adoption accelerated in the 2010s, I hit a limitation with traditional configuration management: these tools were designed to configure existing systems, not provision the infrastructure itself.

This realization marked a key insight: Configuration Management focuses on maintaining the state of existing systems (installing packages, managing services), while Infrastructure as Code (IaC) targets the provisioning and management of the infrastructure resources themselves (VMs, networks, storage). As explained in ["Infrastructure as Code and Configuration Management"](https://www.xcubelabs.com/blog/product-engineering-blog/infrastructure-as-code-and-configuration-management/), they serve different but complementary purposes.

### Terraform: Declaring Infrastructure

My introduction to [Terraform](https://www.terraform.io/) in 2015 was a pivotal moment. Terraform allowed me to define my entire infrastructure in code using HashiCorp Configuration Language (HCL). This wasn't just about running commands on existing servers - it was about creating the servers themselves through code.

The power of Terraform's state-based approach became immediately apparent. Terraform tracks the resources it manages, enabling it to calculate precisely what needs to change during updates. This state management gave me unprecedented control over infrastructure changes.

Even more importantly, codifying infrastructure brought software development practices to infrastructure: version control, peer reviews, automated testing. When a junior engineer proposed a production change, we could review the exact changes in a pull request before anything was deployed.

```hcl
# filepath: /path/to/terraform_example.tf
resource "aws_instance" "example" {
  ami           = "ami-0c55b159cbfafe1f0"
  instance_type = "t2.micro"

  tags = {
    Name = "example-instance"
  }
}
```

### Pulumi: Programming Infrastructure

More recently, I've worked with [Pulumi](https://www.pulumi.com/), which allows the use of familiar programming languages like Python or Go for infrastructure definition. This approach particularly appealed to teams who already have experience with these languages.

The ability to use full programming languages for infrastructure has been transformative for complex environments. Constructs like loops, conditionals, and functions that were awkward in Terraform's DSL are natural in Pulumi. However, as noted in ["Fundamental challenges with Infrastructure as Code"](https://itnext.io/fundamental-challenges-with-infrastructure-as-code-imply-the-language-doesnt-matter-41030475c296), the specific language may matter less than ecosystem factors and team familiarity.

```python
# filepath: /path/to/pulumi_example.py
import pulumi
from pulumi_aws import ec2

example_instance = ec2.Instance('example-instance',
    instance_type='t2.micro',
    ami='ami-0c55b159cbfafe1f0',
    tags={
        'Name': 'example-instance',
    })
```

## Development and Testing Tools in the Configuration Management Ecosystem

### Vagrant: Virtual Environments for Development and Testing

While working with these various tools, I quickly realized I needed a way to test configurations before applying them to production. This is where [Vagrant](https://www.vagrantup.com/) became essential.

Vagrant automates the creation and management of development environments. I would define a virtual machine's configuration in a single file (Vagrantfile), and Vagrant handles then creation and provisioning of that VM. This lets me test my Ansible playbooks or Terraform configurations in isolation before applying them to production.

What makes Vagrant particularly valuable is its integration with multiple virtualization providers and provisioners. I can write a Vagrantfile that specifies not just the OS but also how to configure it using my preferred configuration management tool.

I've also used Vagrant for onboarding. New team members can run a single command and get a fully functioning development environment that matches our production setup, eliminating the dreaded "it works on my machine" problem.

```ruby
# filepath: /path/to/Vagrantfile
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.provision "shell", inline: <<-SHELL
    echo "example configuration content" > /etc/example.conf
  SHELL
end
```

### Packer: Building Standardized Machine Images

As my infrastructure grew more complex, I found that starting from a base OS and applying configuration management every time was inefficient. I needed pre-configured machine images that would be consistent across environments. Enter [Packer](https://www.packer.io/).

Packer automates the creation of identical machine images for multiple platforms from a single source configuration. Instead of starting with a vanilla OS and applying configuration, I would build an (golden) image with the necessary software already installed.

This approach brought several benefits: reduced deployment time (since much of the configuration is baked in), improved consistency (all instances start from the same base), and the ability to test the image building process separately from deployment.

My typical workflow was at the time to use Packer to build Golden Images, Terraform to provision infrastructure, and Ansible to handle final application configuration. This combination provided a clean separation of concerns while leveraging the strengths of each tool.

```json
// filepath: /path/to/packer_example.json
{
  "builders": [
    {
      "type": "amazon-ebs",
      "region": "us-west-2",
      "source_ami": "ami-0c55b159cbfafe1f0",
      "instance_type": "t2.micro",
      "ssh_username": "ubuntu",
      "ami_name": "example-ami"
    }
  ],
  "provisioners": [
    {
      "type": "shell",
      "inline": ["echo 'example configuration content' > /etc/example.conf"]
    }
  ]
}
```

## Specialized Tools for Bare Metal Management

### MAAS: Metal as a Service

Cloud environments made infrastructure provisioning easier, but many organizations still need to manage physical servers. This is where specialized bare metal provisioning tools like [MAAS (Metal as a Service)](https://maas.io/) come in.

MAAS, developed by Canonical, treats physical servers like virtual machines in the cloud. It provides a way to manage a large number of physical machines by creating a resource pool. Machines can be provisioned automatically, used as needed, and then released back into the pool when no longer required.

What makes MAAS powerful is its comprehensive approach: it handles automatic discovery and registration of devices, BMC (IPMI, RedFish) and PXE automation, and integration with configuration management tools like Ansible and Salt.

The workflow is elegant: when a new server is connected to the network, MAAS discovers it, powers it on via IPMI or RedFish, boots it using PXE, and registers it in the inventory. You can then allocate the server to a specific workload, and MAAS will provision it with the appropriate OS and configuration.

### Foreman: Complete Lifecycle Management

Another tool I've used for bare metal is [Foreman](https://www.theforeman.org/), an open-source lifecycle management tool for physical and virtual servers. It handles provisioning, configuration, and monitoring throughout a server's lifecycle.

Foreman integrates with multiple provisioning methods and configuration management tools. For bare metal, it uses PXE boot and MAC address identification. You create host entries, specify the MAC address, and provision automatically.

This integration with tools like Puppet, Chef, Salt, and Ansible allows using a single tool for both provisioning and configuration management - particularly valuable in heterogeneous environments with both physical and virtual machines.

### OpenStack Ironic: Bare Metal as a Service

For organizations using OpenStack, [Ironic](https://www.openstack.org/software/releases/victoria/components/ironic) provides bare metal provisioning capabilities within the OpenStack ecosystem. Ironic is designed to provision physical machines instead of virtual machines.

Ironic interacts with other OpenStack services: Nova for scheduling and API management, Glance for managing disk images, and Neutron for network configuration. The key components include the API service, conductor service, and inspector service, which together provide a complete solution for bare metal management.

I've used Ironic in environments already invested in OpenStack. The integration with other OpenStack services makes it easier to manage both virtual and physical infrastructure using a single set of tools.

## Configuration Management Today: The Hybrid Reality

### How We Actually Use These Tools in 2025

In 2025, most organizations (including mine) use a hybrid approach combining IaC for provisioning with configuration management for system setup. This isn't theoretical - it's how real infrastructure teams operate daily.

As explained in ["Configuration-as-Code: Principles and Best Practices"](https://configu.com/blog/what-is-configuration-as-code-cac-and-5-tips-for-success/), "Infrastructure as Code (IaC) is about automating the provisioning of infrastructure... Configuration-as-Code (CaC) is about managing the settings and parameters that an application or system component needs to function correctly".

In practice, I use Terraform to provision and manages cloud infrastructure resources - creating VMs, networks, storage, and security groups. While Ansible for all the bare metal infrastructure, whatever servers, storage or network devices.

This hybrid approach offers several advantages:

1. Clean separation of concerns
2. Flexibility to use different tools based on their strengths
3. Simplified workflows where each tool does what it does best

### Why Ansible Remains Relevant in the IaC Era

Among configuration management tools, Ansible has shown remarkable staying power. Its agentless architecture, simplicity, and flexibility have allowed it to adapt to changing practices.

The introduction of [Ansible Automation Platform](https://www.redhat.com/en/technologies/management/ansible) with features like automation mesh has further enhanced its relevance. Automation mesh enables distributing automation workloads across hybrid environments, making Ansible suitable even for distributed, cloud-native deployments.

What's particularly exciting is that IBM (which [already owns Red Hat](https://www.redhat.com/en/about/press-releases/ibm-closes-landmark-acquisition-red-hat-34-billion-defines-open-hybrid-cloud-future) and Ansible) [recently acquired HashiCorp](https://www.hashicorp.com/en/blog/hashicorp-officially-joins-the-ibm-family), the makers of Terraform, for $6.4 billion in 2024. This move shows that even the big players recognize the natural synergy between these tools. I'm pretty confident that over time, IBM will bring Terraform and Ansible even closer together as a unified offering. They've already mentioned how Terraform's infrastructure provisioning capabilities combined with Ansible's configuration management creates a powerful one-two punch for hybrid cloud environments. It's like having the best of both worlds under one roof - and honestly, it makes perfect sense given how many of us already use them together in our daily workflows.

As a practical example, I use Ansible alongside Terraform for managing some on-prem Kubernetes clusters. Ansible provisions the cluster infrastructure, while Terraform configures the application deployment, and day-2 operations.

### Are we done yet?

Far from it!

Meet [System Initiative](https://www.systeminit.com/), a new tool from Chef founder Adam Jacob released in 2024. At its core, System Initiative introduces a new approach. Rather than defining infrastructure through static code files, System Initiative creates a living model that simulates entire infrastructure stack, which is a large departure from statically defined configurations of nowadays mainstream tools. Is it here to stay? Only time will tell. For one tool that succeeded, we have hounders that did not.s

There are many others to come, many smaller and bigger issues that we need to address, while still AI is lurking at us.

## Practical Takeaways for Today's Infrastructure Engineers

The evolution from basic tools to sophisticated platforms represents a fundamental transformation in infrastructure management. Rather than one paradigm replacing another, we've seen an expansion of our toolkit, with different tools addressing different needs.

In my current role, I use multiple tools together:

- [Packer](https://www.packer.io/) for creating standardized machine images
- [Terraform](https://www.terraform.io/) for provisioning infrastructure resources
- [Ansible](https://www.ansible.com/) for configuring bare metal devices

This hybrid approach recognizes that no single tool is perfect for everything. By combining strengths, we create more efficient, consistent, and scalable workflows.

If you're just starting your journey into modern infrastructure management, I'd recommend these resources:

- [Terraform: Up \& Running](https://www.terraformupandrunning.com/) by Yevgeniy Brikman
- [Ansible for DevOps](https://www.ansiblefordevops.com/) by Jeff Geerling
- [Infrastructure as Code](https://infrastructure-as-code.com/) by Kief Morris
- [The Phoenix Project](https://itrevolution.com/product/the-phoenix-project/) for understanding the "why"

For experienced engineers, the key takeaway isn't which specific tool to use, but understanding the principles that make these tools effective. As our field continues to evolve, which is part of the fun! The tools will change, but the core principles of automation, consistency, and code-driven infrastructure will remain essential to manage modern systems at scale.

Keep it running smoothly!

> This is part of series of articles about my perspective of 25 years in infrastructure engineering [read more]({{< relref "/tags/25years/" >}})
