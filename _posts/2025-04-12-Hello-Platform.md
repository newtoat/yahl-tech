---

title: Hello, Platform!
description: How does my homelab run
date: 2025-04-12
categories: [Homelab, Overview]
tags: [introduction, overview, platform]     # TAG names should always be lowercase
media_subpath: /assets/posts/Hello-Platform

---

## Introduction
Similar to the previous post on infrastructure, this post is going to dive into the services that support my homelab and the tools that I use.

I have included links to the official sites / repos where possible. I plan to write an individual post for each application listed here (plus many others) and will be uploading the relevant files / manifests to a repo on my GitHub.
> You can find the repo here - [Coming Soon!]()
{: .prompt-tip}

>Note that I have decided to add the files to the repo one at a time rather than all at once, so that I can tidy everything up and make sure it's all nice and presentable
{: .prompt-info}

## Operating Systems
I mentioned in the infrastructure post that I am running Proxmox on the physical hardware to host the homelab, and TrueNAS for the storage. But that isn't really suitable for running applications.

### Virtual Machines
All of the Virtual Machines are running Ubuntu 22.04. Some of these were manually installed and configured, but the majority have been created using Hashicorp Packer and Proxmox templates. This made a huge difference in ensuring consistency between VM for things like user accounts, ssh hardening and auto-update configuration. 

### Kubernetes
Strictly speaking, this is an orchestration tool rather than an Operating System but I'm detailing it here as it feel right. Most of my services are running in Kubernetes containers and the handful that aren't will be migrated over soon enough.
I'm currently on Kubernetes 1.30 - I had spent a weekend upgrading everything from a much older version, including changing the CSI providers, only for 1.31 to drop the week after. 

I should really spend the time updating the cluster to 1.32, but I want to update the way I am running Kubernetes soon anyway. So I'll probably hang fire and just jump a couple of versions by migrating straight to a new cluster. (more details at the bottom of the page, in the future plans section)

### Docker
Again, this isn't really an Operating System. To be honest, I only have Immich and Authentik running on the Docker VM. My intention was to migrate all my services to Docker and then migrate everything to Kubernetes. But as I started it just felt like an unnecessary step, so I started moving things straight to Kubernetes.

I'll probably keep a Docker VM around as it's much easier to spin up a new container for testing before creating the manifest files to run it in Kubernetes.

## Automation

### Packer
This is the only bit of automation I can say is fully up and running in my environment. I've used Packer extensively for customising Windows Server installations with a previous job, so leveraging it to configure Linux servers was a bit of a no-brainer.

It's currently limited to Ubuntu 22.04 servers, but that's fine as that's all I have. It would be nice to also have templates for Ubuntu desktop and Windows Servers / Desktops, but realistically it's not that often I spin those up. If I find that I'm making installs more than once a month then I'll definitely build out some workflows.

The Ubuntu workflows include customisations to:
- Install latest updates
- Install common packages, including quemu-guest-agent
- Set the timezone to UTC
- Add my SSH keys
- Disable password authentication for remote connections
- Enable passwordless sudo
- Disable swap
- Configure Unattended-Upgrades for regular security patches
- Save as a Proxmox template

The main drawback at the moment is that Packer builds are done through my laptop, rather than a dedicated server. Once I have migrated Gitea to run in Kubernetes, I will be starting to build out GitOps where I'll be creating pipelines to run the packer builds for me.

### Proxmox templates
After 15-20 minutes, I end up with a Template VM in Proxmox which can be cloned to create a brand new VM almost instantly. Combine the customisations set in Packer with a couple more options in Cloud-Config and it's as easy as provisioning a new VM in the Cloud!

### Terraform
I've used a lot of Terraform in the past, and I should really use it more at home. I've not got a good reason why I am not doing so already except that it would be fairly locally managed. Once I've got Gitea setup with Agents and I can make pipelines I'll be looking to manage Proxmox via Terraform.

>[https://registry.terraform.io/providers/bpg/proxmox/latest/docs](https://registry.terraform.io/providers/bpg/proxmox/latest/docs) is the provider I would use to manage Proxmox
{: .prompt-tip}

### Ansible
This is one that I don't know much about and have tried to teach myself a couple of times. Unfortunately I keep finding myself getting distracted with other projects before making much progress. 

My long term goal is to be able to provision a new VM with a Proxmox Template (Made by Packer), add it to a file in Ansible and then kick off an appropriate workbook to finish customising the server. The main one being installing Kubernetes and joining the cluster as a new node, but I'd also like to have Wireguard workbook and a barebones Ubuntu workbook.

### Git
Most of these rely on Git to be able to kick off the automation. I've got an old version of Gitea running on a VM, with no Agents configured. It shouldn't take too long to get the Agents up and running, but I am waiting till it's been migrated to Kubernetes before diving down that automation rabbit hole.

## Core Services
So that's the tools I'm currently using for the homelab as a whole. But how am I hosting things on Kubernetes? Core services are what I deem to be essential for running the cluster.

I've actually used Kubernetes on and off for a few years now. I remember setting up a local cluster many years ago as a Proof of Concept at work, back when Windows node support was still in Beta. I also remember having to manually edit the config files to set up Networking whilst creating the cluster! 

> Windows support was added in 1.14 back in 2016!
{: .prompt-tip}

### Cluster connectivity
Cluster connectivity uses the Calico CNI plugin. I don't have much to say about it other than I have used Calico every time I have set up a Kubernetes cluster, so it made sense to stick with what I know works.

Strictly speaking Calico isn't the best choice for integration with MetalLB, but I got it working and I've never had any issues since setting it up.

>Link for Calico - [https://docs.tigera.io/calico/latest/about](https://docs.tigera.io/calico/latest/about)
{: .prompt-info}

### DNS
This isn't strictly required for Kubernetes as Core-DNS is available out of the box. But it feels important enough to be included here with the other core services.

DNS for my homelab is handled by Blocky. I really like the approach of defining the DNS records as code and being able to easily store the zone file in my git repo. Obviously it's not going to be much help in a disaster, but it's nice to look back through the history and see when records were added and figure out what they are used for. I find it encourages me to tidy up stale records much more as well.

I've not seen many other people using Blocky, but the fact that it's stateless and runs from read only config files appealed to me much more than trying to run Pi-Hole. And lets not even talk about trying to keep the different instances in sync with each other! Don't get me wrong, I love Pi-Hole and have used it for a great many years, starting on a Raspberry Pi before moving it to a VM, but I don't use the UI often enough to warrant the increased pain of trying to run it with high availability. There's even Prometheus metrics and Grafana dashboards available for those lovely graphs.

>Link for Blocky - [https://github.com/0xERR0R/blocky](https://github.com/0xERR0R/blocky)
{: .prompt-info}

>Link for Pi-Hole - [https://pi-hole.net](https://pi-hole.net)
{: .prompt-info}

### Load Balancer
As I'm running this Kubernetes cluster locally, on my own servers, there is no external load balancer to use to be able to connect to services running on the cluster. Enter MetalLB.

MetalLB is pretty much the only option for making load balancer resources work when running Kubernetes clusters on bare-metal. Their page explains the issue much better than I can, I'll not copy it here but go check out their site!

I have three pools of IP Addresses configured in MetalLB at the moment:
- Blocky. Configured with a single IP address which is used to allow DNS traffic on Port 53 to go straight to the Blocky containers
- Nginx. Configured with a single IP address which is routed to the Ingress-Nginx containers, providing access to the applications running in the cluster
- MetalLB. Configured with 3 available IP addresses, this isn't really used for anything but gives me the option of quickly trialing new services that require Load Balancer functionality

>Link for MetalLB - [https://metallb.io](https://metallb.io)
{: .prompt-info}

### Ingress
Ingress is provided by Ingress-Nginx, which uses Nginx to provide a centralised ingress to the services running inside the cluster.
> Not to be confused with the very similarly named Nginx-Ingress...
{: .prompt-danger}

Ingress resources are deployed alongside each of the applications to be exposed, with the ingress class specified as Nginx. Then Ingress-Nginx, which sits and watches the Ingress resources in the cluster, spots the new ingress request and dynamically updates to provide routing to the service. You can also customise properties within Nginx using annotations on the Ingress resource, e.g. max request size.

Install instructions aren't the best, but once you're up and running it all runs nicely. Ingress-Nginx works really well for me, any issues I've had have all been down to missing annotations to configure Nginx appropriately. I've heard good things about Traefik, so I'll give that a whirl at some point. Whether I switch over, I'm not sure.

>Link for Ingress-Nginx - [https://kubernetes.github.io/ingress-nginx/deploy/](https://kubernetes.github.io/ingress-nginx/deploy/)
{: .prompt-info}

>Link for Traefik - [https://doc.traefik.io/traefik/getting-started/install-traefik/](https://doc.traefik.io/traefik/getting-started/install-traefik/)
{: .prompt-info}

### TLS Certificates
Whilst Ingress-Nginx provides the ingress functionality and allows traffic to reach the services inside the cluster, it doesn't automatically provision TLS certificates. Cert-Manager steps in to provide this functionality.

Cert-Manager has a number of issuers that you can use depending on who your registrar is. For me, this is Cloudflare and I am using the DNS validation option.
>Note that one notable provider that doesn't work with Cert-Manager is Namecheap as they don't provide API integrations. You can however, delegate your DNS to a different provider that does and use a DNS validation.
{: .prompt-warning}

### Kubernetes API ingress
Having the Ingress to applications running in the cluster is great for availability because if one of the hosts becomes unavailable, the pods simply move to another host. But for interacting with the cluster itself, you still send requests to one specific host. And if that host is unavailable, you would need to tweak the kube.config file to point to a different host.

To solve this, and make the Kubernetes management plane highly available, in addition to the applications, I've configured keepalived to provide a load-balanced IP which will forward requests to any of the available nodes.
>Link for configuring Kubernetes to use keepalived - [https://github.com/kubernetes/kubeadm/blob/main/docs/ha-considerations.md#options-for-software-load-balancing](https://github.com/kubernetes/kubeadm/blob/main/docs/ha-considerations.md#options-for-software-load-balancing)
{: .prompt-info}

### Persistent Storage
I suppose it's worth touching on what I mean by persistent storage. Each of the Kubernetes nodes has plenty of storage and the containers can have storage mounted to provide persistent storage. However, if the container moves to a different node for whatever reason then it's not going to be able to mount files from the original node. The goal is to have storage that the containers can access regardless of which node it is running on.

As I am running my Kubernetes nodes on Proxmox, I can simply enable the Ceph features of Proxmox and have distributed storage. This works reasonably well as I mentioned in the previous post, but there are definitely some improvements which could be made. I then use the Ceph-CSI plugin to enable Kubernetes to connect to Ceph and use it for storage.
> Link for Ceph-CSI plugin - [https://github.com/ceph/ceph-csi](https://github.com/ceph/ceph-csi)
{: .prompt-info}

Longhorn is an alternative method of making providing distributed storage and runs directly inside your Kubernetes cluster. I'd definitely be using this if I didn't have Proxmox to provide distributed storage.
>Link for **Longhorn** - [https://longhorn.io](https://longhorn.io) 
{: .prompt-info}

### Secrets 
I'm really pleased with my secret solution. I was fearing this would have to wait until I had set up CI/CD pipelines and configure them securely that way. But then I found Bitwarden's Secret Manager and it's Kubernetes Operator. I'm already using Bitwarden for my personal password manager and some homelab passwords, so extending to use Secret Manager was fairly easy.
Secrets are stored securely in Bitwarden, and a Bitwarden container connects via APIs to download and create the specified secrets in the appropriate namespaces. 

> Link for **Bitwarden Password Manager** - [https://bitwarden.com/products/personal/](https://bitwarden.com/products/personal/) and **Bitwarden Secret Manager** - [https://bitwarden.com/help/secrets-manager-overview/](https://bitwarden.com/help/secrets-manager-overview/)
{: .prompt-info}

In order to access the secrets, an API token needs to be passed through. As far as I can tell this must be stored in the namespace you want the secrets to be stored in, which needs to be where the app is. Currently I manually create this credential secret in each namespace that needs it, but I've recently learnt of Kubernetes-Reflector which is able to replicate secrets into other Namespaces. I'm not entirely convinced as to how useful this will be for me considering I'd only use it for the one auth token secret, but I'll give it a whirl and see.

>Link for **Kubernetes-Reflector** - [https://github.com/emberstack/kubernetes-reflector](https://github.com/emberstack/kubernetes-reflector)
{: .prompt-info}

The biggest frustration is that it's only possible to add Secret Manager to the free, team and enterprise subscriptions. And I have a family subscription. Fortunately they have a work around and recommend that you sign up for a new free subscription to use for Secret Manager.

>Note that it is possible to link the subscriptions together so that the user in the family subscription is able to view/control Secret Manager in the free subscription
{: .prompt-tip}

>I've not been able to find Secret Manager anywhere in the Bitwarden desktop app, so I tend to use the browser app for managing Homelab/Kubernetes secrets
{: .prompt-warning}

### Backups
I suppose it's worth touching on how data is stored across my cluster before I explain how it's backed up.
Any application in Kubernetes that requires persistent storage is deployed as a Stateful Set, with the volumes being mounted from Ceph. This is done using Ceph-CSI, specifically with the RBD plugin. 

As to the backups, I'm using Velero. Velero backs up the entire Kubernetes state including all resources and configuration, snapshots the persistent volumes and backs those up too. The backups are sent to MinIO, which has storage on TrueNAS which is in turn backed up. 

>Link to Velero - [https://velero.io](https://velero.io)
{: .prompt-info}

I still need to re-evaluate how I have this set up as it's using up way more storage than I would like and it's not possible for me to have different retention schedules for different services. Currently everything is being backed up in one big schedule and stored for 30 days. Realistically, I should be having multiple backup schedules to separate destination folders so that different retention policies can be applied.

I'm thinking something like:
- Critical data - daily backups and off-site copies. Things like Immich, Paperless-Ngx etc.
- Important data - weekly backups and monthly off-site copies. Things like Lubelogger, Mealie etc.
- Non-important data - weekly backups but no off-site copies. Things the monitoring stack and probably also the security stack (...I'll have bigger issues if they go!)

There would, of course, be some things that aren't backed up at all. Some services I'm running don't store data (it-tools) and some have readily available data (Ollama). I might even do an entire cluster backup every couple of weeks just in case, and would ensure I've got a semi recent backup should I forget to add a service to a schedule.

## Shared Services
Shared services are what I'm deeming as things that are used by multiple applications. Monitoring obviously falls under this as it (Prometheus) pulls metrics from all available applications. Secret management services should probably be classified under here... 

### Monitoring / Prometheus Operator
The monitoring stack I am using is Prometheus / Grafana / AlertManager. I used the Kube-Prometheus project to deploy the stack and provide a decent foundation. Prometheus metrics for an application can be added automatically by defining a ServiceMonitor resource and the Grafana dashboards are stored in ConfigMaps and mounted to the Grafana pod. Alertmanger, I don't actually do much with. I know I should make use of the metrics I have and set up some alerts, but I haven't felt it to be necessary yet. 

>Link to Kube-Prometheus - [https://github.com/prometheus-operator/kube-prometheus](https://github.com/prometheus-operator/kube-prometheus)
{: .prompt-info}

### InfluxDB
I'm including this here only because it relates to monitoring and supports the Proxmox monitoring. I have an instance of InfluxDB running which Proxmox streams metrics to. I've also configured it as a data source in Grafana to be able to visualize the metrics.

> Link to InfluxDB - [https://github.com/influxdata/influxdb](https://github.com/influxdata/influxdb)
{: .prompt-info}

### Redis Operator
This is probably a bit overkill for my needs to be honest. I'm only running two redis databases - one for Blocky, and one for Paperless NGX. I found it when looking into how best to spin up a Redis database for Blocky, and it's worked very well.

>Link to Redis Operator - [https://redis.io/docs/latest/operate/kubernetes/](https://redis.io/docs/latest/operate/kubernetes/)
{: .prompt-info}

## Future plans
The common theme of everything is that there is more that I want to do. And I love that. It gives me plenty to be playing with.

The main future plan is to continue building out this site and putting out somewhat regular posts. I don't want to tie myself into a schedule but I'm aiming for roughly every few weeks. Coming up first will be the services I struggled to get running.

In terms of my homelab itself, there are lots of things I want to do. But they can be broadly split into adding new things and enhancing the stuff I already have.

### Adding new things

#### Centralised logging
I don't necessarily need this as I only have a handful of servers (plus lots of containers), but I haven't used one before so I'd like to. 

#### Security services
I've used SIEM tools before to a degree, but I've never really got down into the nitty gritty of them. Most of the time it's been get them installed and configured and run a few basic tests to validate everything is working. 

Whilst writing this post I've actually installed and set up Wazuh. I'm still figuring it out and trying to tailor it to my environment but I'm looking forward to doing more with it - more on this soon!

> Link to Wazuh - [https://wazuh.com](https://wazuh.com)
{: .prompt-info}

#### Monitor image versions
I guess this may fall under a Kubernetes specific security tool - I'm still looking into what would be best. Essentially I want something that looks at what containers I have running in my cluster, compare them against what's available publicly, and notify me if anything is out of date.

There are a couple of tools that do this for Docker, but I haven't found any that run in a Kubernetes environment. Worst case, I can set something up using a Gitea Pipeline but that means I need to maintain it when I add new images. Ideally I want a tool that just queries what's running, that way I don't miss anything.

### Enhancing the stuff I already have

#### Redeploy Kubernetes VMs
I've currently got Kubernetes running on 3 Virtual Machines, which are all masters and workers. It worked well enough initially, but as I am adding additional workloads like monitoring into the mix I've started to run into a few issues. Disk space is one, which I could admittedly fix by making the disk bigger. But the bigger problem is the fact that the hosts are massively overspecced due to the demands of Ollama. I'll separate that out it to essentially it's own dedicated node and then have 3 more appropriately sized specced nodes. 

#### Kubernetes best practices
This is somewhat of an unknown to me. I think I'm doing things reasonably well, though there are some things I'm choosing not to do at the moment for simplicity - things like restricting networking to namespaces and setting pod / workload priorities. 

I need to research best practices and make an effort to follow them. Ideally I'd like to be able to run some sort of tool across the cluster and have it tell me all the issues with how it's been configured / being used. 

#### Ansible automation
I'd also like to take the opportunity to create Ansible playbooks for turning a fresh Ubuntu server into Kubernetes masters / workers. I'll do this for the other servers I have running but this feels like a good starting point with real benefit.

#### GitOps
Once Gitea has been migrated to run in Kubernetes I'll start spending time restructuring the repo and building out pipelines to deploy changes when I commit files to the repo.

I could do this now but I'm trying to use it as an excuse to force myself to prioritise doing it... it's not been very successful so far.

#### Backups
I touched on it earlier up but I want to revisit my backup strategy. The current method is essentially the "good enough" approach. I need to spend some time figuring out what tiering I want for backup retentions, and then work that into Velero scheduled backups.

#### Traefik / Ingress
I'm currently using Ingress-Nginx which has been working fantastically for me. I've never used Traefik, but I know it's very highly regarded for Kubernetes ingress. I'm not necessarily looking to move completely, at the moment I'm just interested in understanding if I'm missing out on anything by not using it.

> Link to Traefik - [https://github.com/traefik/traefik](https://github.com/traefik/traefik)
{: .prompt-info}

## Thanks
I was going to include details about the applications I am running in my homelab but this has ended up much longer than I was expecting. Some have been mentioned already as they are "core" services that make up my homelab platform. There will be a separate post going up very shortly to cover what applications I'm running.