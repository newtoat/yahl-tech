---

title: Hello, World!
date: 2025-03-08
categories: [Homelab, Overview]
tags: [homelab]     # TAG names should always be lowercase
pin: true

---

## Introduction

As with any new project, it only feels right to start with a Hello World! post.

I have run a homelab in some form for many years now, and has been instrumental in my career progression ...so why the blog, and why now? Before I answer that, allow me to quickly introduce myself:
- My name is **Andrew**
- My Cloud provider of choice is **Azure**
- My current role is **Senior Cloud and Infrastructre Architect** (as of Dec 2023)
- My previous roles can be grouped into SysAdmin, Cloud Engineer, and DevOps Engineer

I may give a full walkthrough of my career at some point (or maybe just publish a sanitised CV) but during my previous few jobs I've tackled on-prem to Cloud migrations, full domain rebuilds, and oh so much automation. The two standout automation projects were:
1. Rebuilding AVD hosts on a monthly basis from scratch using Hashicorp's Packer

    > *Yes, this is now available natively in Azure, but I had scripted it using Azure DevOps pipelines!*

2. Building out a no code internal deployment platform

    > *Essentially, this used the front-end elements of our ticketing system to take details from non-technical users, feed that in to some ADO Pipelines which would run terraform to deploy the resources. Some were simple like adding a new rule to an NSG, some were complex like deploying a new Virtual Machine with logic to decide whether to use an existing Virtual Network/Subnet or create a new one for example.*
    
As you can see, most of my previous roles have been very technical. And the homelab has helped me stay ahead of what I have been doing at work - practicing domain migrations, learning Terraform and Docker / containerisation. However, as I have been settling into being a Cloud Archtect, I have found that I don't get time to be as technical as I used to be. And I miss it. So the nerdy answer is that I miss being technical and breaking things.

The other answer for why I am starting a blog is simply to improve my writing skills. Being an Architect means a lot more decision making and planning, and more importantly, putting that on paper - or, as is more often the case, on a Word / PowerPoint document. Whether that's high level details to senior stakeholders or low level designs to colleagues, I'm doing a lot more diagramming and writing. 

So there we go, starting a blog feels like a natural progression to my homelab to match where I am with my career. And importantly, still lets me play with the latest and greatest.

---

## What can you expect from this blog

Initially, I want to create an article diving into exactly what the current state of my Homelab is. Followed by more details on my plans for the Homelab - why I want to do them and how I'm hoping to achieve them. And probably also a post about how my Homelab has evolved over the years, I've tried a lot of things and not everything worked out...

After that, I'll start working through the different services that I am running, how I've configured them, and any other thoughts I have. The first couple will likely be **Blocky** ([GitHub](https://github.com/0xERR0R/blocky)), **MetalLB** ([GitHub](https://github.com/metallb/metallb)) and **Coder** ([Github](https://github.com/coder/coder)) as I struggled to find many articles to help me when I was setting them up!

---

## Overview of my Home Lab

### Hardware Components
- **Compute:** 
    - 3x Dell Optiplex 3080 Micro PCs all configured with a i5-10500T processor and 32GBs of RAM
    - 2x Rasperry Pi 3b (Currently unused)
    - 2x Raspbery Pi 4b (Currently unused)
    - A HP DL360 Gen 9 (also currently unused, but I got it for free from the company my partner worked for)

- **Storage:** 
    - A HP Microserver N40L serving as a NAS
        - 2x SSDs for 500GBs of (relatively) fast storage
        - 2x HDDs for 8TB of slow storage
    - The three Dell servers all have
        - A 256GB SSD for local storage
        - a 1TB NVME SSD for Ceph clustered storage (configured via Proxmox)
    - Also worth mentioning that I have some offsite backups replicated to BackBlaze

- **Networking:** 
    - A managed switch for the main networking between the servers
    - A simple unmanaged switch for Ceph / Proxmox traffic on a dedicated Subnet
    - 2x TP Link Deco XE75 - One plugged into the router, and one connected to the Homelab in my office
    - 2x TP Link M5 - For access around the house
    - Virgin Media business broadband and router (...because I could)

### Software Stack
- **Operating Systems:** 
    - **Proxmox** for the three compute servers
    - **TrueNAS Scale** for the NAS
    - Ubuntu LTS for the Virtual Machines
    
- **Hosting methods:** 
    - **Kubernetes** powers most of the Services running in my Homelab
    - **Docker** is used for some quick testing and a couple of services I haven't migrated to Kubernetes yet
    - Theres also a handful of services running in Virtual Machines on Proxmox

- **Core Services:**
    - DNS is dealt with by **Blocky** ([Github](https://github.com/0xERR0R/blocky))
    - Loadbalancer by **MetalLB** ([Github](https://github.com/metallb/metallb))
    - Ingress by **Ingress-Nginx** ([Github](https://github.com/kubernetes/ingress-nginx))
    - S3 style object storage by **MinIO** ([Github](https://github.com/minio/minio))
    - Kubernetes backups by **Velero** ([Github](https://github.com/vmware-tanzu/velero))
    - Monitoring by **Prometheus** and **Grafana** ([Github](https://github.com/prometheus-operator/kube-prometheus))
    - Simple up/down notificatoins via a Discord webhook in **Uptime Kuma** ([Github](https://github.com/louislam/uptime-kuma))

- **Some of my favourite Services:**
    - **Coder** ([Github](https://github.com/coder/coder)) for web base IDEs. I have a few different ones depending on what I'm doing and they are fantastic.
    - **Hoarder** ([Github](https://github.com/hoarder-app/hoarder)) for minimising the amount of tabs I have open at any given time
    - **Bitwarden Secrets Manager** ([Github](https://github.com/bitwarden/sm-kubernetes)) for secret management
    - **Ollama** ([Github](https://github.com/ollama/ollama)) / **OpenWebUI** ([Github](https://github.com/open-webui/open-webui)) for local LLMs... even if it takes anywhere from 20 to 90 minutes to get a response!

---

## Future Plans
Focusing on the infrastructure / hosting side of things, I have a few goals in mind for the future which I can split into two categories - Short term goals and Long term goals. 

### Short term goals
There are a handful of things I want to do sooner rather than later; mostly around optimising what I currently have set up. And preparing myself for the bigger long term goals:
- Finish migrating services to run in Kubernetes
- Add 2.5Gbe to the Homelab
- Migrate to a new NAS
- Add an additional Kubernetes node with GPU capabilities
- Install and play with any software that looks interesting

### Long term goals
These are more like strategic goals to work towards. They don't have clear definitions but are things I want to work towards and be more consistent with:

- Establish this blog and put out regular content
- Rebuild the Homelab with a secure by design architecture
- Implement GitOps properly 
- Automate provisioning servers with Ansible

### Future services 
There are a few core services missing from my Homelab that I hope to add at some stage:
- A SysLog server
- Some sort of unified security / SIEM server

---

