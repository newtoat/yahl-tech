---

title: Hello, Applications!
description: What applications do I run
date: 2025-04-13
categories: [Homelab, Overview]
tags: [introduction, overview, applications]     # TAG names should always be lowercase
media_subpath: /assets/posts/Hello-Applications

---

## Introduction
This post serves as a continuation of the previous, where I outlined the what makes up my homelab platform. Now I a going to run through what applications I am running. I will be doing a more detailed post for each application listed here, and uploading the files to my Github repo.

> The repo can be found here - [Coming Soon!]()
{: .prompt-tip}

>Note that I have decided to add the files to the repo one at a time rather than all at once, so that I can tidy everything up and make sure it's all nice and presentable
{: .prompt-info}

## Applications
Only a quick summary of each, these are the applications I have running right now. Most of them are running in Kubernetes, but there are some running in Docker and on regular Virtual Machines.

### Kubernetes
Multiple nodes for high availability makes this my preferred way to run applications.

#### Actual Budget
Actual budget is a finance/budgeting software based on the envelope method. And it has great reporting tools to see exactly how much I'm spending on my homelab...
>Link to Actual Budget - [https://github.com/actualbudget/actual](https://github.com/actualbudget/actual)
{: .prompt-info}

#### Change Detection
Want to know when a web page is updated? Change Detection does that and it's got a whole host of other features too. I mainly use it to keep any eye on changes to documentation pages, but I did make use of it to buy a Steam Deck when they were in short supply.
>Link to Change Detection - [https://github.com/dgtlmoon/changedetection.io](https://github.com/dgtlmoon/changedetection.io)
{: .prompt-info}

##### Coder
I cannot rave enough about Coder. I use it to create dedicated workspaces which can be accessed remotely, either through the browser or via VSCode remote connections. 

I recently upgraded my laptop and was somewhat underwhelmed because there was so little I needed to do to get back up to speed.
>Link to Coder - [https://github.com/coder/coder](https://github.com/coder/coder)
{: .prompt-info}

#### Donetick
This is a new addition (having been added whilst writing this post). I've not used it enough to decide if it'll stick around but it seems like a nice way of tracking repeating household tasks / chores.
>Link to Donetick - [https://github.com/donetick/donetick](https://github.com/donetick/donetick)
{: .prompt-info}

#### Drawio
A very powerful web-based diagraming tool. I use Visio a lot for work, but for quick diagrams that don't need the same level of polish, Drawio is the tool for the job. There's a public endpoint that everyone can use, but I can self-host it so why not?
>Link to Drawio - [https://github.com/jgraph/drawio](https://github.com/jgraph/drawio)
{: .prompt-info}

#### Foundry Virtual Tabletop
As the name implies, Foundry VTT is a virtual tabletop that can be played in the browser. It provides a visual backdrop for your tabletop games (like D&D or Blades in the Dark) when everyone is together, or even allow you to play entirely on-line.
>Link to Foundry VTT - [https://foundryvtt.com](https://foundryvtt.com)
{: .prompt-info}

>Note that Foundry VTT doesn't have an official docker image, but the community favourite is this one by Felddy - [https://github.com/felddy/foundryvtt-docker](https://github.com/felddy/foundryvtt-docker)
{: .prompt-info}

#### KaraKeep
The application formerly known as Hoarder. It's frustrating that the developer has been forced to rebrand, but I really like the new name.

I have a problem with keeping way too many tabs open - I used to hover between 80 and 100 tabs open at any given time on my phone. Karakeep / Hoarder has helped with this and I'm usually down to around 50 these days. Still work to go but I'm improving!

> Link to KaraKeep - [https://github.com/karakeep-app/karakeep](https://github.com/karakeep-app/karakeep)
{: .prompt-info}

#### Homepage
As you can imagine, this is a simple site intended to be the startup page of your browser of choice. I've got a handful of common bookmarks set up, as well as links to all the services I host in the homelab. There's even integrations to Kubernetes - to check the status of the pods, and integrations with most applications to provide relevant statistics.

> Link to Homepage - [https://gethomepage.dev](https://gethomepage.dev)
{: .prompt-info}

#### IT Tools
There are way too many tools available in here, but suffice to say it comes in incredibly handy and I often use it for work related stuff. Converters, formatters, generators, cheat sheets - it has it all!

> Link to IT Tools - [https://github.com/CorentinTh/it-tools](https://github.com/CorentinTh/it-tools)
{: .prompt-info}

#### LubeLogger
Perhaps the most unusually named application on this list - this is actually a car maintenance tracker. I use it to keep track of all the services / upgrades / repairs I do to my cars, and you can even generate a nice PDF report detailing the entire history for a car.

> Link to LubeLogger - [https://github.com/hargata/lubelog](https://github.com/hargata/lubelog)
{: .prompt-info}

#### Mealie
A recipe manager that includes functionality to plan meals for the week and generate a shopping list for everything you need. Point any publicly available recipe page to it and it'll grab the details and add it to your collection. There's also AI functionality to normalise units of measurement from recipes.

> Link to Mealie - [https://github.com/mealie-recipes/mealie/](https://github.com/mealie-recipes/mealie/)
{: .prompt-info}

#### MinIO
Essentially a locally hosted AWS S3 bucket. It is capable of high performance object storage - I mainly use it for storing the backups Velero takes.

> Link to MinIO - [https://github.com/minio/minio](https://github.com/minio/minio)
{: .prompt-info}

#### Ollama
Ollama allows me to run a Large Language Models (LLMs) on my own hardware.

> Link to Ollama - [https://github.com/ollama/ollama](https://github.com/ollama/ollama)
{: .prompt-info}

#### Open Web UI
Open Web UI provides a web interface to interact with Ollama and the LLMs it's running. With their powers combined I have something that more resembles an AI pen-pal than an AI chatbot!

> Link to Open Web UI - [https://github.com/open-webui/open-webui](https://github.com/open-webui/open-webui)
{: .prompt-info}

#### Paperless-ngx
Paperless-ngx is a document management tool. I found this shortly after re-mortgaging our house and the pain of finding all the documents from 2 years prior. I've scanned in all the documents that used to sit in _the big binder_ and they are now fully searchable and viewable. 

> Link to Paperless-ngx - [https://github.com/paperless-ngx/paperless-ngx](https://github.com/paperless-ngx/paperless-ngx)
{: .prompt-info}

#### PortcheckerIO
Portchecker is a fairly simple tool that checks whether a port is open for a host. As it's running inside a pod in Kubernetes, it also lets me test cluster DNS as well as regular network DNS.

> Link to Portchecker.io [https://github.com/dsgnr/portchecker.io](https://github.com/dsgnr/portchecker.io)
{: .prompt-info}

#### Stirling PDF
A collection of tools for manipulating PDFs from the browser. It doesn't get much use, but i'm glad I have it when I need to use it. I got so tired of spending 20 minutes editing a PDF with a free tool I found online, only to be forced to sign in to actually save the changes I had made.

> Link to Stirling PDF - [https://github.com/Stirling-Tools/Stirling-PDF](https://github.com/Stirling-Tools/Stirling-PDF)
{: .prompt-info}

#### Uptime Kuma
I've had this running for a long time now, it does one thing and it does it well. Ping hosts to make sure they are online, and optionally send notifications to alert you.

> Link to Uptime Kuma - [https://github.com/louislam/uptime-kuma](https://github.com/louislam/uptime-kuma)
{: .prompt-info}

#### Wazuh
A locally hosted SIEM tool. This is a fairly new addition to my environment, I've added all my servers to it (and oh boy has it found a lot of vulnerabilities) but I still need to spend some time configuring it and baselining it to my environment. For example, the Kubernetes hosts are throwing a huge number of flags due to the fact they are running containers. I suspect there will be some simple allow / excludes to configure but I haven't looked into it yet.

> Link to Wazuh - [https://wazuh.com](https://wazuh.com)
{: .prompt-info}

### Docker
I had intended to initially migrate all my services to run in Docker before moving on to hosting them in Kubernetes, but once I started it seemed easier to jump straight to Kubernetes.

#### Authentik
Authentik is my identity provider of choice to log into applications using the same credentials. Admittedly, not everything is connected up at the moment, but as part of migrating it to run in Kubernetes I'll be setting up connections to all applications that support it.

> Link to Authentik - [https://github.com/goauthentik/authentik](https://github.com/goauthentik/authentik)
{: .prompt-info}

#### Immich
Self hosted photo store similar to Google Photos. I used to use this a lot but stopped when I accidental duplicated all the photos in my library - To be clear this wasn't a fault with Immich, this was me messing things up in Google Photos which then synced across.

> Link to Immich - [https://github.com/immich-app/immich](https://github.com/immich-app/immich)
{: .prompt-info}

### VMs
I've got two applications running as an entire Virtual Machine rather than as a container somewhere. With the exception of Wireguard these will be migrated to run in Kubernetes at some point.

#### Gitea
My git provider of choice at home - it mostly runs and feels like GitHub, only it's running locally. I did set up a runner when they were first released but I never spent the time to set up any pipelines.

> Link to Gitea - [https://about.gitea.com/products/gitea/](https://about.gitea.com/products/gitea/)
{: .prompt-info}

#### Jellyfin
A streaming website for all the media I have stored locally. For a long time now I have been storing a copy of my physical media in digital format, mostly because I'm lazy and don't want to get up and change DVDs when binging CSI Crime Scene Investigation. But it's also useful for playing media I own on devices that don't have a DVD player.

> Link to Jellyfin - [https://github.com/jellyfin/jellyfin](https://github.com/jellyfin/jellyfin)
{: .prompt-info}

#### Wireguard
The VPN tool that lets me connect to my home network from anywhere. I did use OpenVPN for a long time but Wireguard wins out most of the time because it allows for roaming - not a big deal most of the time, but I left it on enough that it became a mild inconvenience.

I'm unsure whether I want to containerise Wireguard, it feels like it would only complicate the networking side of things. Whereas I can manage what devices on the VPN can access very easily at the moment with firewall rules in Proxmox.

> Link to Wireguard - [https://www.wireguard.com](https://www.wireguard.com)
{: .prompt-info}

> Link to OpenVPN - [https://openvpn.net](https://openvpn.net)
{: .prompt-info}

## Offsite
A quick side note before diving into what's running at my parents house. 

My dad is a retired engineer, he's very familiar using CAD tools as he used them for many many years.

My mum is very crafty... she loves to scrapbook and make cards etc. She also has an online shop where she sells the things she creates, as well as selling supplies for people to make their own. Since dad got a 3D printer, mum has put him to work with designing and printing lots of stuff.

### AutoCAD VDI
A few years ago my dad got a 3D printer for Christmas and he loves it. His laptop isn't the best, but it does the job and allows him to model things up in AutoDesk and print them. I tried setting up a server for him to use as a remote desktop but the latency was too high for it to be of any benefit. If I had a server running at their house it may be worth revisiting as performance would be much better locally.

> It's an Ender 3S if anyone interested
{: .prompt-info}

### Octoprint
Dad's biggest frustration with his 3D printer was that he was using his laptop downstairs while the printer was upstairs in a spare bedroom. Meaning he had to keep walking up and down the stair with an SD card to change what was printing.

Enter Octoprint. He has not stopped raving about it since I set it up last year. The ability to access the printer through his browser and start new prints has been a game changer. There's even a webcam feed of the print bed meaning he can keep an eye on things through his phone.

> Link to Octoprint - [https://github.com/OctoPrint/OctoPrint](https://github.com/OctoPrint/OctoPrint)
{: .prompt-info}

### Manyfold
Following on the theme of 3D printing, I've also set up a Manyfold instance for him to store his models on. He hasn't got round to using it in earnest, but he was excited at the idea of it when seeing what it was capable of.

> Link to Manyfold - [https://github.com/manyfold3d/manyfold](https://github.com/manyfold3d/manyfold)
{: .prompt-info}

## Future plans
As ever, there is so much that I want to do. Maybe I'll add a page to the site to keep track of what I have planned for the future. For now, these are the main things I have in mind.

### Migrate remaining applications
Most applications are already running in Kubernetes, but there are a handful still left to migrate - Gitea / Authentik / Immich.

### Logging and SIEM
I've touched on this already in the Platform post - I want to explore having a centralised logging server and a SIEM tool. The SIEM tool has been set up via Wazuh but I still need to configure it and make it more useful.

### Image versioning
I guess this may fall under a Kubernetes specific security tool - I'm still looking into what would be best. Essentially I want something that looks at what containers I have running in my cluster, compare them against what's available publicly, and notify me if anything is out of date.

There are a couple of tools that do this for Docker, but I haven't found any that run in a Kubernetes environment. Worst case, I can set something up using a Gitea Pipeline but that means I need to maintain it when I add new images. Ideally I want a tool that just queries what's running, that way I don't miss anything.

## Thanks
I've greatly enjoyed writing these couple of introduction posts as it's allowed me to reflect on what I've accomplished already. I look forward to building out this blog and delving deeper into each application / service, and I hope you'll stick around as I do.

> I'll keep this page updated and add links to any future posts I create.
{: .prompt-tip}