---
post_title: 'Introducing the ScaleIO Framework for Apache Mesos'
layout: post
published: true
---
Back in May at EMC World 2016, I had hinted at a project relating to [Software Defined Storage](https://en.wikipedia.org/wiki/Software-defined_storage) (SDS) and [Mesos Frameworks](http://mesos.apache.org/documentation/latest/architecture/) that I was really interested in working on. After fulfilling my other project obligations, I recently found some time to really work on this and yesterday I hit a good milestone on this project that warrants a release and an announcement... and no its not the next iPhone. It's the [ScaleIO Framework](https://github.com/dvonthenen/scaleio-scheduler) for Apache Mesos!

![The new iPhone](https://github.com/dvonthenen/blog/raw/master/images/iphone.jpeg)

### Awesome! What does it do?

The [ScaleIO Framework](https://github.com/dvonthenen/scaleio-scheduler) installs and configures the [ScaleIO](https://www.emc.com/storage/scaleio/index.htm) components necessary to provision and consume ScaleIO block storage on Mesos Agent nodes. This means that existing Agent nodes in your Mesos cluster will then have the capability to create, mount, and unmount ScaleIO volumes for tasks run on that node. That isn't the cool part as there are existing orchestration tools like [Vagrant](https://www.vagrantup.com/), [Puppet](https://puppet.com/), or etc that already accomplish this.

The real value add is that as Agent nodes are onboarded or added to the Mesos cluster, the ScaleIO Framework will be instantly made aware of these nodes and immediately transform those nodes to have the ability to provision and consume external persistent volumes from ScaleIO!

### Why is this Revolutionary?

To discuss this we need to look back to the past. Initially, one of the biggest limitations of resource schedulers like Apache Mesos, Swarm, Kubernetes, and etc is that they lack the access to persistent external storage that is required to run production applications. Persistent storage was limited to the local disks on these compute nodes (or in the Mesos case Agent nodes) and failure of those nodes represented losing that data. Projects like [REX-Ray](https://github.com/emccode/rexray) and [mesos-module-dvdi](https://github.com/emccode/mesos-module-dvdi) then came along that significantly closed the gap to providing access to external persistent storage that these storage platforms provide.

The next (r)evolution that is made capable by this Framework is that by combining a Software Defined Storage platform and Application Schedulers, we can transform all Mesos Agent nodes to have access to persistent external storage **by default**. This brings us full circle to my EMC World session [Deep Dive With Mesos & Persistent Storage For Applications](https://www.emcworldonline.com/2016/connect/sessionDetail.ww?SESSION_ID=2720) in which we talked about how 2 layer scheduling can make complicated applications, such as Software Defined Storage, easier to use by simplifying the installation, configuration, and operational complexities these systems bring with them. That in turns makes these applications easy to consume by completely abstracting the complexities away from its users.

![Kiss it simple stupid](https://github.com/dvonthenen/blog/raw/master/images/simpicity.jpg)

### Let's see this in action...

I have put together a video demo of this ScaleIO Framework in action. You can get a permanent link to the video on my [YouTube]() channel. Without further ado, check out the video below!

<iframe width="560" height="315" src="https://www.youtube.com/embed/DYfS99GMqwU" frameborder="0" allowfullscreen></iframe>

### Info, status and TODOs

You can find more information on the ScaleIO Framework's [github page](https://github.com/dvonthenen/scaleio-scheduler). This project will be moved over to the EMC {code} github repository soon. You can look for it [here](https://github.com/emccode/) when that move happens.

This first version is more of a proof of concept or demonstration to highlight the capabilities of combining Software Defined Storage together with a Scheduling platform that offers 2 layer scheduling. Subsequent versions will add significantly more features towards making this framework production worthy.

Here are a list of TODOs in the works:
- Add CentOS/RHEL support
- Add CoreOS support
- Add for the ability to provision the entire ScaleIO cluster include the MDM management nodes from scratch
- Allow for more customization of the ScaleIO rollout
- Manage the entire lifecycle (upgrades, maintenance, etc) of all nodes in the ScaleIO cluster automatically
- Manage the health of a ScaleIO cluster by monitoring for critical events (Performance, Almost Full, etc)

### Where do we go from here?

It turns out that a lot of the functionality mentioned above is already in progress as I had to scale back some of the functionality in this first release to make [MesosCon Europe](http://events.linuxfoundation.org/events/mesoscon-europe) which happens to be going at the time of writing this. By the way, some of my fellow [EMC {code}](http://emccode.com/) team members are at the event so please stop by our booth to talk about this ScaleIO Framework or anything Apache Mesos.

Looking forward, I am hoping that this project will generate some buzz and create more interest in how storage ties into container schedulers. I think there is a lot more that the storage space has to offer and I think subsequent versions of this ScaleIO Framework can easily demonstrate the important role that 2 layer scheduling or subscheduling of resources plays in the ecosystem.
