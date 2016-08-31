---
ID: 85
post_title: >
  Introducing the ScaleIO Framework for
  Apache Mesos
author: dvonthenen
post_date: 2016-08-31 14:10:14
post_excerpt: ""
layout: post
permalink: >
  http://dvonthenen.com/2016/08/31/introducing-the-scaleio-framework-for-apache-mesos/
published: true
---
Back in May at EMC World 2016, I had hinted at a project relating to [Software Defined Storage][1] (SDS) and [Mesos Frameworks][2] that I was really interested in working on. After fulfilling my other project obligations, I recently found some time to really work on this and yesterday I hit a good milestone on this project that warrants a release and an announcement... and no its not the next iPhone. It's the [ScaleIO Framework][3] for Apache Mesos!

![The new iPhone][4]

### Awesome! What does it do?

The [ScaleIO Framework][3] installs and configures the [ScaleIO][5] components necessary to provision and consume ScaleIO block storage on Mesos Agent nodes. This means that existing Agent nodes in your Mesos cluster will then have the capability to create, mount, and unmount ScaleIO volumes for tasks run on that node. That isn't the cool part as there are existing orchestration tools like [Vagrant][6], [Puppet][7], or etc that already accomplish this.

The real value add is that as Agent nodes are onboarded or added to the Mesos cluster, the ScaleIO Framework will be instantly made aware of these nodes and immediately transform those nodes to have the ability to provision and consume external persistent volumes from ScaleIO!

### Why is this Revolutionary?

To discuss this we need to look back to the past. Initially, one of the biggest limitations of resource schedulers like Apache Mesos, Swarm, Kubernetes, and etc is that they lack the access to persistent external storage that is required to run production applications. Persistent storage was limited to the local disks on these compute nodes (or in the Mesos case Agent nodes) and failure of those nodes represented losing that data. Projects like [REX-Ray][8] and [mesos-module-dvdi][9] then came along that significantly closed the gap to providing access to external persistent storage that these storage platforms provide.

The next (r)evolution that is made capable by this Framework is that by combining a Software Defined Storage platform and Application Schedulers, we can transform all Mesos Agent nodes to have access to persistent external storage **by default**. This brings us full circle to my EMC World session [Deep Dive With Mesos & Persistent Storage For Applications][10] in which we talked about how 2 layer scheduling can make complicated applications, such as Software Defined Storage, easier to use by simplifying the installation, configuration, and operational complexities these systems bring with them. That in turns makes these applications easy to consume by completely abstracting the complexities away from its users.

![Kiss it simple stupid][11]

### Let's see this in action...

I have put together a video demo of this ScaleIO Framework in action. You can get a permanent link to the video on my [YouTube]() channel. Without further ado, check out the video below!

<iframe width="560" height="315" src="https://www.youtube.com/embed/DYfS99GMqwU" frameborder="0" allowfullscreen></iframe> 
### Info, status and TODOs

You can find more information on the ScaleIO Framework's [github page][3]. This project will be moved over to the EMC {code} github repository soon. You can look for it [here][12] when that move happens.

This first version is more of a proof of concept or demonstration to highlight the capabilities of combining Software Defined Storage together with a Scheduling platform that offers 2 layer scheduling. Subsequent versions will add significantly more features towards making this framework production worthy.

Here are a list of TODOs in the works: - Add CentOS/RHEL support - Add CoreOS support - Add for the ability to provision the entire ScaleIO cluster include the MDM management nodes from scratch - Allow for more customization of the ScaleIO rollout - Manage the entire lifecycle (upgrades, maintenance, etc) of all nodes in the ScaleIO cluster automatically - Manage the health of a ScaleIO cluster by monitoring for critical events (Performance, Almost Full, etc)

### Where do we go from here?

It turns out that a lot of the functionality mentioned above is already in progress as I had to scale back some of the functionality in this first release to make [MesosCon Europe][13] which happens to be going at the time of writing this. By the way, some of my fellow [EMC {code}][14] team members are at the event so please stop by our booth to talk about this ScaleIO Framework or anything Apache Mesos.

Looking forward, I am hoping that this project will generate some buzz and create more interest in how storage ties into container schedulers. I think there is a lot more that the storage space has to offer and I think subsequent versions of this ScaleIO Framework can easily demonstrate the important role that 2 layer scheduling or subscheduling of resources plays in the ecosystem.

 [1]: https://en.wikipedia.org/wiki/Software-defined_storage
 [2]: http://mesos.apache.org/documentation/latest/architecture/
 [3]: https://github.com/dvonthenen/scaleio-scheduler
 [4]: https://github.com/dvonthenen/blog/raw/master/images/iphone.jpeg
 [5]: https://www.emc.com/storage/scaleio/index.htm
 [6]: https://www.vagrantup.com/
 [7]: https://puppet.com/
 [8]: https://github.com/emccode/rexray
 [9]: https://github.com/emccode/mesos-module-dvdi
 [10]: https://www.emcworldonline.com/2016/connect/sessionDetail.ww?SESSION_ID=2720
 [11]: https://github.com/dvonthenen/blog/raw/master/images/simpicity.jpg
 [12]: https://github.com/emccode/
 [13]: http://events.linuxfoundation.org/events/mesoscon-europe
 [14]: http://emccode.com/