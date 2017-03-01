---
post_title: 'Applications That Fix Themselves'
layout: post
published: true
---
I know that in my last blog post I said I would be talking (and probably announcing) the FaultSet functionality planned for the next release of the [ScaleIO Framework](https://github.com/codedellemc/scaleio-framework). As all things in the world of technology and software, things don't always go as planned. So today I am here to talk about some stuff relating to the Framework that will be in my speaking session at [SCaLE 15x](https://www.socallinuxexpo.org/scale/15x) this Saturday March 4th at 3pm in Ballroom F of the Pasadena Convention Center.

![SCaLE 15x Logo](https://github.com/dvonthenen/blog/raw/master/images/15x_logo_lg.png)

This new functionality at face value seems straight forward, but the implications start to open the doors to like next level thought kinda stuff. Ok ok ok. I may have oversold that a little, but the idea itself is still pretty cool and I am super excited to talk about here.

### Just make it happen. I don't care how!

Just yesterday, I release the ScaleIO Framework [version 0.3.1](https://github.com/codedellemc/scaleio-framework/releases/tag/v0.3.1) which has a *functionality preview* \*\*cough\*\* experimental \*\*cough\*\* for a couple of features that I think is cool. The first feature, although not as interesting, will probably be the most useful immediately to people that **want** use ScaleIO... starting from a bare Mesos cluster, you can provision the entire ScaleIO storage platform in an highly available 3-node configuration from scratch and have all the storage integrations, like [REX-Ray](https://github.com/codedellemc/rexray) and [mesos-module-dvdi](https://github.com/codedellemc/mesos-module-dvdi), installed automatically.

![Easy Street](https://github.com/dvonthenen/blog/raw/master/images/EasyStreetSign.jpg)

In case you missed it... without having to know anything about [ScaleIO](https://www.emc.com/storage/scaleio/index.htm?pid=glossary-page-serversan-02122016), you can deploy an entirely software-based storage platform that will give your Mesos workloads the ability to persist application data seamlessly, that is globally accessible, and all the while making your apps highly available. This abstracts the complexities of the entire storage platform and transforms it into a simple service where you can simply consume storage from. As far as your concerned, the storage platform natively came with Mesos and the first app you deploy can consume ScaleIO volumes from day one. If you want more details on how to make that happen, please check out the [documentation](http://scaleio-framework.readthedocs.io/en/stable/user-guide/experimental/).

### TODO: How is everyone doing this morning?

I think the second *functionality preview* \*\*cough\*\* experimental \*\*cough\*\* in the 0.3.1 release is perhaps the most compelling in story but may be less useful in practice (at least for now). I have always been fascinated by this idea that applications when they run into trouble can go and fix themselves. We often call this self-remediation. In practice, that has always been a pipe dream but there is some really cool infrastructure in the form of [Mesos Frameworks](http://mesos.apache.org/documentation/latest/app-framework-development-guide/) that make this idea a possibility.

![It's not going to happen](https://github.com/dvonthenen/blog/raw/master/images/hangover-not-going-to-happen.jpg)

So this second feature comes from my days as both a storage and backup user... where I get the dreaded storage array is full notification. This typically entails getting another expander shelf for your storage array (if you are lucky enough to have expansion capability), populate disks in the expansion bay, and then configure the array. In the age of Clouds and DevOps, anything is possible and provisioning new resource is only as far as an API call away.

The idea is that as our storage pool starts to approach full, we can provision more raw disk in the form of EBS volumes to contribute to the storage pool since we exist in the cloud or in this case AWS. That is exactly idea behind this feature. Sounds cool yea?!?! If you are interested in what this idea brings, I urge you to check out the [user guide](http://scaleio-framework.readthedocs.io/en/stable/user-guide/experimental/), try it out, provide input and feedback!

### Where to go next...

So I hope the FaultSet functionality is just around the corner along with the support for [CoreOS](https://coreos.com/), or what they are now calling [Container Linux](https://coreos.com/products/container-linux-subscription/), since a lot of the stuff coming out of [Mesos](http://mesos.apache.org/) and [DC/OS](https://dcos.io/) is now based on that platform. Let us know if you want more surrounding Mesos and ScaleIO Framework by hitting me up in our community slack channel at [{code} community](http://community.codedellemc.com/). Additionally if you are in the Los Angeles area this week, I would highly recommend stopping by [SCaLE 15x](https://www.socallinuxexpo.org/scale/15x) in Pasadena, catching some of the sessions, and stopping by the {code} booth to continue the conversation.
