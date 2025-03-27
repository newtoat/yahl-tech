---

title: Hello, Infrastructure!
description: What does my homelab run on and how does it all work
# date: 2025-03-08
categories: [Homelab, Overview]
tags: [introduction, overview, infrastructure]      # TAG names should always be lowercase
media_subpath: /assets/posts/Hello-Infrastructure   # Make it easier to reference media if needed

---

## Introduction
Before talking about the applications and services, lets start with the infrastructure. It's fairly simple at the moment, but I hope to change that in the next few months.

I've lots of ideas and lots of things I want to play with, and I don't expect to be able to do them as quickly as I would like to. Nevertheless it's good to have the noted down somewhere to keep me somewhat accountable and stay working towards them.

## Core Servers
### Proxmox
Three Dell Optiplex 3080 Micro servers, which are identical in every way... except that one of them also has a Wi-Fi card.

The specs are:
- Intel i5-10500T CPU
- 32GB RAM
- 256GB OS Drive
- 1Tb Samsung NVME storage drive
- 1x Built in Gigabit ethernet port
- 1x USB Gigabit ethernet adapter

For a grand total of 36 cores and 96GB RAM.

The three are clustered together within Proxox for easier management operations between the nodes. And Ceph has been configured to use the 1Tb NVME drives, providing reasonably fast (*more on this below) distributed storage across the cluster. Combined together, this allows for almost immediate fail overs of VMs between the nodes.

### TrueNAS
My storage server is quite old at this point - having been purchased second hand in, with my best guess, sometime around 2017. It is a HP Proliant Microserver N40L. 

The specs are:
- AMD Turion II Neo N40L CPU
- 8GB RAM
- 240GB OS Drive
- 2x 250GB SSD / Fast storage (Raid 0)
- 2x 8Tb HDD / Slow storage (Raid 1)

Nothing terribly fancy going on with the NAS. There are two Pools setup for fast and slow storage. The fast storage Pool has a Dataset for storing ISO files for installing new VMs in Proxmox, and a Dataset passed through to MinIO for object storage. While the slow storage Pool has Datasets for media storage, Proxmox backups and a shared folder for important documents.

## Inactive Servers
There are a handful of other bits and pieces lying around which are not in use at the moment. These have all been in use at some point in the past couple of years, which has led me to following hostname convention:

- prox**min**-x for the Raspberry Pi's
- prox**mid**-x for the Microservers
- prox**max**-x for the full size server

### Raspberry Pi's
At one point I had 2x Raspberry Pi 4b and 3x Raspberry Pi 3b+. The two 4b boards have gone off to my brother and my dad, while the 3b+'s are sat in a drawer waiting for me to do something with them.
I've thought about using one of them as a secondary DNS server but I've not had many issues running DNS in Kubernetes with built in resiliency. Still, probably a good idea to set up at some stage.

I first started trying to run a Kubernetes cluster on these booting from a USB flash drive, but the performance was dismal to say the least. It barely ran a freshly installed cluster, and if you tried to spin up any apps it would quickly fall into a backoff loop from running out of IOPS. I can't remember whether I was using full blown Kubernetes or if I had been using K3s - I've definitely used K3s before with great success.

Not one to say no, I then configured them to boot from the storage server over the network via iSCSI. This gave much better IOPS and they ran like this for a long while. I can't remember if there was a specific reason I stopped using them, it was probably just that I bought the first of the Proxmox hosts and swapped over to using that instead. I may return and do this again at some point - it was a very fun project to set up and get going.

I have a fantastic little case to store four of these together:

<div class="row" style="justify-content: space-around; display: flex;">
    <div class="col">
        <img src="Pi-Stack-Front.JPG" alt="Front view of a case to hold 4 Raspberry Pi's" width="350" style="padding:1rem; min-width:300px;"  loading="lazy">
    </div>
    <div class="col">
        <img src="Pi-Stack-Rear.JPG" alt="Rear view of a case to hold 4 Raspberry Pi's, showing off the integrated fans" width="350" style="padding:1rem; min-width:300px;" loading="lazy">
    </div>
</div>


### HP Proliant DL360 Gen 9
This was given to me from the company my partner was working from at the time. I think it had just gone out of basic support so they bought a new server for the office and they asked if anyone wanted it. I very excitedly met her after work to collect my new server... though we did get told off by a traffic warden for stopping in the loading bay. In my defense, those things are heavy and difficult to carry solo!

As fun as it is to have this at home, it's far too loud (and power hungry) to have running in my office 24/7. So it tends to sit as a show piece more than anything else these days.


## Networking
First off, I want to say that the network architecture isn't great. The homelab started fairly small with 1 Promxox host and the NAS so it wasn't super important to segregate and secure it properly. As servers have been added over time i've just sort of added them and never gone back and improved the network design. Now that Proxmox has added SDN (Software Defined Networking) functionality I will be revamping the setup soon, separating the homelab into it's own/multiple subnets. Kubernetes and Docker at least run their containers in a separate network.

I'll skip over how connectivity works for the moment as I think that suits the application overview post more. For now, here is a physical connectivity diagram:

![Physical network diagram](/infrastructure.drawio.svg)
_Diagram showing the networking within my home. Note that unless otherwise specified, connections are Gigabit ethernet_

### Internet
Home internet is provided by Virgin Media, specifically Virgin Media Business. We had no need for a phone line or tv package, so the sales rep offered business broadband to us - This worked out as 400Mbps for ~£33/month and came with an upgraded router compared to regular. The deals are a lot more comparable now compared to a few years ago.

### Wi-Fi
Connected to the router is a TP Link Deco XE75, which is configured to be the access point(s) for the network. There are 3 additional Deco's around the house providing mesh wifi with a rock solid connection. 
>Hard wired would be better, but running cables isn't really an option for the layout of our house. Still, there haven't been any issues even with two of us working from home on a regular basis.
{: .prompt-info}

There's an XE75 in my office, which is configured with a 6Ghz backbone down to the main XE75 connected to the router. Then a couple of Deco M5's in various locations - One next to the CCTV box which requires an ethernet connection and one in the kitchen pushing wifi out into the garden.

### VPN
I am using Wireguard to connect to my homelab when I'm away. Combine this with web-based VSCode and I can lab from anywhere on any device! 

In fact, I did this recently when I was in Dublin for work - I was using an iPad and a travel keyboard. Connecting home via Wireguard and then running VSCode in the browser through Coder.

![Photo of an iPad being used to access remotely](Remote-access.jpeg)
_A recreation of using the iPad to access the homelab remotely_

## Storage
### TrueNAS
As mentioned above, TrueNAS has two pools of storage configured with SSDs and HDDs. These are then shared over the network via NFS to the relevant services, with allowlists configured for restricting access to the specific devices that need it.

Honestly, performance isn't great. The N40L is pretty old at this point and it has served me well. My main issues come from running ZFS on 8GBs of RAM and it being a fairly lightweight CPU. I could look at upgrading it to 16GBs of RAM, but honestly I think I'll save the money and just replace the thing in the coming months.

### Ceph
Ceph is installed on each of the Proxmox nodes, configured with the 1Tb NVME drive. I have two main use cases for Ceph, with separate pools for each:
- Proxmox VMs; some VMs are running on Ceph for high availability reasons. I can migrate them to a new node in about 3 seconds
- Kubernetes storage; having distributed storage means I can have persistent storage available regardless of what node a container is running on

Performance is... ok. I'm definitely not getting the most out of the NVME drives, but I've not found myself wanting more performance at any point so it doesn't bother me too much. I definitely have a bottleneck by running Ceph over a (dedicated) 1GbE network (when the recommendation is at least 10GbE). I will upgrade to 2.5GbE when I get time to have a proper play and benchmark things - Best case I see an improvement, worst case I prove it's limited by disk rather than the network.
> Worth calling out that there are reports of Ceph having substantial issues running on consumer SSDs. While I've not benchmarked my setup, I don't think this is a significant issue for me currently.
{: .prompt-info}

> This was a fantastic write up I found when reading up on potential performance issues with Ceph - [https://static.xtremeownage.com/blog/2023/proxmox---building-a-ceph-cluster](https://static.xtremeownage.com/blog/2023/proxmox---building-a-ceph-cluster)
{: .prompt-tip}

### MinIO
MinIO is a recent addition to my homelab, providing a (supposed) high performance object storage. It certainly runs well, though I wouldn't call my specific setup a high performance object store. As TrueNAS isn't able to run the MinIO container image, I am instead running the container in my Kubernetes cluster with a NFS share connection to the TrueNAS fast storage pool. This absolutely isn't the best approach and I'm sure there are many things slowing down performance but it does the job for now.

## Backups
I'm reasonably happy with my backups at the moment, though I'm keeping my eyes open for ways to improve it.

### Proxmox backups
The easiest method of backing up my homelab, and also the most expensive in terms of storage usage. Most VMs have weekly backups taken and uploaded to TrueNAS. The exceptions are those that are used for testing, or the Kubernetes nodes where the storage is decoupled anyway.

I have looked at Proxmox Backup Server, but haven't felt it necessary for my needs. Sure, deduplication is nice but I've plenty of storage still and I'm pretty happy backing up directly to an NFS share. I may reconsider this in the future, who knows.

### Kubernetes
Most of my workloads are now running in Kubernetes, and those that aren't will be soon enough. Again, I'll run through the applications / services in a supporting post, but my ideal would be near enough everything running in Kubernetes with as few VMs to maintain as possible.

All applications that require persistent storage have a block device dynamically provided by Ceph. Rather than backing up Ceph, because the best ways I found were to replicate to another Ceph cluster, I am instead taking backups of each block device running in Kubernetes. I'm currently using Velero [(Link)](https://velero.io) to do this, though I've heard good things about Veeam's Kasten [(Link)](https://www.veeam.com/products/cloud/kubernetes-data-protection.html) and it includes nicer built in dashboards.

### Offsite backups
Currently this is configured with 'good enough' ideology. I don't have any super critical files that I would be devastated to lose, just a bunch of stuff that I would be mildly inconvenienced at losing. I'm pretty happy with the risk I'm taking at the moment - All of my important files are backed up and replicated to Backblaze. From a homelab perspective, I have my git repo locally on a couple of devices with regular usage, but also being backed up to Backblaze which should let me recover reasonably well in the event of losing everything.

To improve backups I would like to set up a small NAS at my parents house. I have a couple services running for them so it would be good to make things easier for them to access and allow me to store all my data offsite for next to nothing (other than the hardware cost).

## Offsite
Speaking of running things at my parents house...

### Raspberry Pi 1
My brother commandeered one of the Raspberry Pi 4b's I had. He used it to build a Magic Mirror, which is hung up in his bedroom and still in use.

### Raspberry Pi 2
I've also donated the other Raspberry Pi 4b to my dad. It's connected to his 3D printer, running Octoprint and ManyFold.

## Future plans
I've already alluded to some of the improvements I'd like to make. In no particular order:
### Storage server
My TrueNAS server is working ok at the moment, but it's definitely not the best. The main benefit would come from being able to run MinIO directly on the host and mounting the storage directly, theoretically improving the performance of Kubernetes Backups.

Broad requirements beyond a more modern CPU are:
- At least 16GBs of RAM, ideally 32GB
- An extra SSD for caching on the HDD storage pool
- At least 6 drive bays / slots (OS Drive + 3x SSD + 2x HDD)
- Multiple ethernet ports for link aggregation. This should allow for better performance when being accessed by multiple nodes at the same time
- Some 2.5GbE ports for future proofing, probably via PCIE expansion card and I could go to 10GbE even further in the future?

### Graphics enabled server
I'd like to have a dedicated server with a graphics card installed. I can't add one to the existing servers due to the form factor, so it makes sense to have a dedicated server in a larger case.

There's a couple of I could do with a graphics capable server:
- Ollama. This is currently running using memory which brings scheduling challenges in Kubernetes, and also takes an absolute age to get a response. A couple applications I'm hosting have AI functionality available so I'd love to get this running better
- Jellyfin transcoding. This is working fine using CPU but it would be nice to free up some additional resources.
- Gaming. I have a Steam Deck which is a surprisingly capable little machine, but I'd love to be able to run games on proper hardware up in my office and stream it to any device on my network. 

> Some early testing has shown I should have good enough Wi-Fi speeds to get a solid connection with minimal latency.
{: .prompt-tip}

The way I see it working would be running as a Kubernetes host, potentially as a VM in Proxmox. With each of the ideas above running as separate containers, all sharing access to the GPU. It seems to be possible to share the GPU between multiple containers and I'm hoping they can all have access. Given that there shouldn't be much overlap in when they'll each be running, they should effectively have the full GPU available for use. Will it work? I've no idea, I'll have to set it up and find out.

### Revamp network architecture
Having everything on the same network isn't great, and considering I'm a Cloud Architect I feel somewhat guilty about the state of my homelab. I want to split things out, have the entire lab on a separate network and move the Virtual Machines to Proxmox's SDN.

### Networking speed
I don't necessarily need it, but I should be able to move to 2.5GbE fairly easily considering you can get USB adapters that run at pretty much full speed. This should bring improvements to the Proxmox Cluster / Ceph and backup operations to the TrueNAS server.

### JetKVM
This is actually already in progress. I was late to back it on Kickstarter, but I am in the March delivery batch so should be receiving one in the next couple of weeks.

It's not too difficult for me to physically access the servers given they are in my office, but I'd still rather not deal with the cables if I can help it. It would be really convenient for setting up any new devices or reinstalling the current servers - I'm actually still running Proxmox 8.1.3. 

### Upgrade Proxmox 
As above, I'm still running Proxmox 8.1.3. Proxmox 8.3 is out now and I expect 8.4 will drop in the next month or two. Whilst I could do an in place upgrade, I want to do a fresh install of them to clean up any unused config and make sure I have all additional changes documented with a future goal of template the deployment with Ansible.

### Automation utopia
The ultimate goal for me is to have the ability to image a server using the JetKVM device. Then kick off Ansible runbooks to customise and finish setting up the server for normal usage.
> This is mostly aimed at the Proxmox hosts, I'm not sure if I feel comfortable automating TrueNAS to the same level. TrueNAS's config can be exported to a config file anyway
{: .prompt-tip}


## Thanks
That's it for now. There's a lot of info up there but hopefully it sets the scene reasonably well. This post focussed on the infrastructure powering my homelab, stay tuned for a follow up post focusing on how it runs and what I'm hosting!