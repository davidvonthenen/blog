---
ID: 100
post_title: Applications that Fix Themselves
post_name: applications-that-fix-themselves
author: david
post_date: 2017-03-03 08:31:00
layout: post
link: >
  https://dvonthenen.com/2017/03/03/applications-that-fix-themselves/
published: true
tags:
  - Apache Mesos
  - Conferences
  - External Storage
  - Framework
  - Mesos
  - SCaLE
  - SCaLE 15x
  - ScaleIO
  - ScaleIO Framework
  - Storage
categories:
  - Apache Mesos
  - Conferences
  - ScaleIO
---
<p>I know that in my last blog post I said I would be talking (and probably announcing) the FaultSet functionality planned for the next release of the <a href="https://github.com/codedellemc/scaleio-framework">ScaleIO Framework</a>. As all things in the world of technology and software, things don't always go as planned. So today I am here to talk about some stuff relating to the Framework that will be in my speaking session entitled <a href="https://www.socallinuxexpo.org/scale/15x/presentations/how-container-schedulers-and-software-defined-storage-will-change-cloud">How Container Schedulers and Software Defined Storage will Change the Cloud</a> at <a href="https://www.socallinuxexpo.org/scale/15x">SCaLE 15x</a> this Saturday March 4th at 3pm in Ballroom F of the Pasadena Convention Center.</p>

<p><img src="https://github.com/dvonthenen/blog/raw/master/images/15x_logo_lg.png" alt="SCaLE 15x Logo" /></p>

<p>This new functionality at face value seems straight forward but the implications start to open the doors to next level thought kinda stuff. Ok ok ok. I may have oversold that a little, but the idea itself is still pretty cool and I am super excited to talk about here.</p>

<h3>Just make it happen. I don't care how!</h3>

<p>Just this week, I released the ScaleIO Framework <a href="https://github.com/codedellemc/scaleio-framework/releases/tag/v0.3.1">version 0.3.1</a> which has a <em>functionality preview</em> &#42;&#42;cough&#42;&#42; experimental &#42;&#42;cough&#42;&#42; for a couple of features that I think is cool. The first feature, although not as interesting, will probably be the most useful immediately to people that <strong>want</strong> use ScaleIO but was turned off from the installation instructions... starting from a bare Mesos cluster, you can provision the entire ScaleIO storage platform in an highly available 3-node configuration from scratch and have all the storage integrations, like <a href="https://github.com/codedellemc/rexray">REX-Ray</a> and <a href="https://github.com/codedellemc/mesos-module-dvdi">mesos-module-dvdi</a>, installed automatically.</p>

<p><img src="https://github.com/dvonthenen/blog/raw/master/images/EasyStreetSign.jpg" alt="Easy Street" /></p>

<p>In case you missed it... without having to know anything about <a href="https://www.emc.com/storage/scaleio/index.htm?pid=glossary-page-serversan-02122016">ScaleIO</a>, you can deploy an entirely software-based storage platform that will give your Mesos workloads the ability to persist application data seamlessly, that is globally accessible, and make your apps highly available. This abstracts the complexities of the entire storage platform and transforms it into a simple service where you can simply consume storage from. As far as any user is concerned, the storage platform natively came with Mesos and the first app you deploy can consume ScaleIO volumes from day one. If you want more details on how to make that happen, please check out the <a href="http://scaleio-framework.readthedocs.io/en/stable/user-guide/experimental/">documentation</a>.</p>

<h3>The Sky is Falling!! Do Something?!?!</h3>

<p>I think the second <em>functionality preview</em> &#42;&#42;cough&#42;&#42; experimental &#42;&#42;cough&#42;&#42; in the 0.3.1 release has perhaps the most compelling story but may be less useful in practice (at least for now). I have always been fascinated by this idea that applications, when they run into trouble, can go and fix themselves. We often call this self-remediation. In reality, that has always been a pipe dream but there is some really cool infrastructure in the form of <a href="http://mesos.apache.org/documentation/latest/app-framework-development-guide/">Mesos Frameworks</a> that make this idea a possibility.</p>

<p><img src="https://github.com/dvonthenen/blog/raw/master/images/hangover-not-going-to-happen.jpg" alt="It's not going to happen" /></p>

<p>So this second feature comes from my days as both a storage and backup user... where I get the dreaded storage array is full notification. This typically entails getting another expander shelf for your storage array (if you are lucky enough to have expansion capability), populate disks in the expansion bay, and then configure the array to accept this new raw capacity. In the age of Clouds and DevOps, anything is possible and provisioning new resource is only as far as an API call away.</p>

<p><img src="https://github.com/dvonthenen/blog/raw/master/images/anything-is-possible.jpg" alt="Anything is possible" /></p>

<p>The idea is that as our ScaleIO storage pool starts to approach full, we can provision more raw disks in the form of EBS volumes to contribute to the storage pool. Since we exist in the cloud or in this case AWS that is only an API call away. That is exactly idea behind this feature... to live in a world where applications can self-remediate and fix themselves. Sounds cool yea?!?! If you are interested in more information about this feature, I urge you to check out the <a href="http://scaleio-framework.readthedocs.io/en/stable/user-guide/experimental/">user guide</a>, try it out, provide input and feedback! And if you happen to be at <a href="https://www.socallinuxexpo.org/scale/15x/presentations/how-container-schedulers-and-software-defined-storage-will-change-cloud">SCaLE 15x</a> this week, I will be doing this exact demo live! BONUS: You can watch that video demo that was performed at SCaLE here:</p>

<iframe width="560" height="315" src="https://www.youtube.com/embed/kTDYF8ma1_s" frameborder="0" allowfullscreen></iframe>

<h3>Where to go next...</h3>

<p>So I hope the FaultSet functionality is just around the corner along with the support for <a href="https://coreos.com/">CoreOS</a>, or what they are now calling <a href="https://coreos.com/products/container-linux-subscription/">Container Linux</a>, since a lot of the stuff coming out of <a href="http://mesos.apache.org/">Mesos</a> and <a href="https://dcos.io/">DC/OS</a> is now based on that platform. Let us know if you want more surrounding Mesos and the ScaleIO Framework by hitting me up in our <a href="http://community.codedellemc.com/">{code} community slack</a> channel #mesos. Additionally, if you are in the Los Angeles area this week, I would highly recommend stopping by <a href="https://www.socallinuxexpo.org/scale/15x">SCaLE 15x</a> in Pasadena, catch some of the sessions, and stop by the {code} booth in the expo hall to continue the conversation.</p>
