---
ID: 45
post_title: Looking Back at SCaLE x14
post_name: looking-back-at-scale-x14
author: david
post_date: 2016-02-02 08:30:54
layout: post
link: >
  https://dvonthenen.com/2016/02/02/looking-back-at-scale-x14/
published: true
tags:
  - Charms
  - Conferences
  - Docker
  - Golang
  - HashiCorp
  - IoT
  - Juju
  - Juju Charms
  - Mentoring
  - Nomad
  - Personal
  - SCaLE
  - Snappy
  - Ubuntu
  - Ubuntu Core
  - Unikernels
categories:
  - Conferences
---
<p>For those that don't know what <a href="https://www.socallinuxexpo.org/scale/14x">SCaLE</a> is... SCaLE stands for the <a href="https://www.socallinuxexpo.org/scale/14x">Southern California Linux Expo</a> and this marked the 14th year the conference has been held which happened to be in Pasadena, CA on Jan 21-24. This was the first time I have attended SCaLE and I have to say that it was quite refreshing going to a conference where the primary purpose wasn't trying to sell you product but rather ideas and open source projects of interest. A lot of this is due to a very community driven focus which encompassed everything from session selection, a large volunteer staff, and etc.</p>

<p>There were a number of sessions and tracks ranging from Ubucon (everything Ubuntu), PostgreSQL, MySQL, Apache Bigtop, Open Source in Education, Unikernels, Robotics using Golang (pictured below) and etc. I took it upon myself to take a slice out of each pie to get a good feel for what the conference had to offer. Like everything in life, there was a range of the good, the bad and the ugly... but I would have to say there wasn't much, if any, in the latter category. For reference, you can get a copy of the conference schedule <a href="https://www.socallinuxexpo.org/scale/14x/schedule/thursday">here</a> as a sampling of what types of sessions SCaLE provides.</p>

<p><img src="https://raw.githubusercontent.com/dvonthenen/blog/master/images/gobot.jpg" alt="Gobot - Robots Powered by Golang" /></p>

<p>The other significant difference is that audience or the make up of the attendees in this conference is significantly different than traditional conferences backed by big money which in this instance was predominately developers, DevOps peeps, and sysadmins. Gone are the sales and marketing people with the heavy focus on landing deals with private rooms off to the side where contracts are being drawn up. If you are looking to network with other developers and the actual users of a particular technology, this is a fantastic place to connect with those people.</p>

<p>Without further ado, here are some notes from sessions I found interesting. It should also be noted that I have included a lot of the session links in this blog as many of the slide decks are available on those pages.</p>

<h3>IoT and Snappy Ubuntu Core</h3>

<p>This session was titled <a href="https://www.socallinuxexpo.org/scale/14x/presentations/internet-things-gets-snappy-ubuntu-core">Internet of Things gets 'snappy' with Ubuntu Core</a> and its main focus was introducing people to the idea of <a href="http://www.ubuntu.com/cloud/snappy">Snappy Ubuntu Core</a> which Ubuntu is pushing as the solution for building applications in the cloud and on embedded devices. Think of it as a minimal Ubuntu operating system built using the same libraries as traditional Ubuntu just with a much smaller footprint wrapped with a convenient package management that can orchestrate installation and upgrades. The IoT demo was an application built using snappy on a raspberry pi with an IP camera that can do facial tracking. Pretty cool! You can find more information about Snappy Ubuntu Core <a href="https://developer.ubuntu.com/en/snappy/start/">here</a>.</p>

<p><img src="https://raw.githubusercontent.com/dvonthenen/blog/master/images/iot.jpg" alt="IoT using Snappy Ubuntu Core on a Raspberry Pi" /></p>

<h3>Juju Charms</h3>

<p>This was my first exposure to Juju Charms. I had heard the name being thrown around before but didn't have the time to take a look at the offering. The session <a href="https://www.socallinuxexpo.org/scale/14x/presentations/writing-your-first-juju-charm">Writing your first Juju Charm</a> was a well presented introduction for first time users. The main takeaway is that Juju models relationships with other applications encapsulated in what is called a charm and that those charms, for example MySQL, are created, maintained, and tuned by subject experts. Pulling those vetted charms helps provide a solid foundation to build your applications on. Dependencies in charms are automatically pulled in and the likeness was compared that to the layers of an onion; you just layer in the applications you want. Then when pulling together your solution, its simply dragging and connecting these charms within the UI.</p>

<p>In writing your own charm, the method of hooking these applications together is done by an event driven engine. For example, your application contained within your own charm doesn't get installed until the http server started and the MariaDB started event are received. Overall a good session. You can find more information about Juju <a href="https://jujucharms.com/about">here</a> and <a href="https://jujucharms.com/store?type=charm">here</a>.</p>

<h3>Docker and Unikernels</h3>

<p>Although what I am going to write about here wasn't a session at SCaLE per se, but speakers from both the Docker and Unikernel presentations at this special edition of the <a href="http://www.meetup.com/Docker-Los-Angeles/events/228120991/">Docker LA Meetup SCaLE x14 Edition</a> had sessions at the conference. The first speaker was Jérôme Petazzoni from <a href="http://www.docker.com/">Docker</a> (pictured on the right). We discussed Swarm and the advances made in the latest release specifically around mulit-network orchestration between containers instances to satisfy the cluster use case. I did enjoy the fact that the discussion was almost completely driven by demonstration, but from the sounds of known issues even walking into this demo, it makes me think that this isn't ready for prime time yet. I definitely appreciate the candor and honesty from the presenter about these issue; speaks to his professionalism.</p>

<p>The second half of the meetup quickly switched gears to talk about the Docker acquisition of <a href="http://unikernel.com/">Unikernel Systems</a> who is the primary driver of the open source ecosystem of <a href="http://unikernel.org/">unikernel.org</a>. Presenting a 101 type course on unikernels was Amir Chaudhry and Richard Mortier (pictured on the left). The session was quite fascinating considering I had not heard of the term unikernel until Docker made the announcement that morning. In a nutshell, the idea of a unikernel is such that you only take the various parts of a particular stack, say networking, that you need in order for your application to run. This implies that the stack is modular and has clear separation of concerns. The claim is that this type of operating system has:</p>

<ul>
<li>a smaller footprint in terms of size</li>
<li>better security because they have a lower penetration profile</li>
<li>better performance because unnecessary services aren't running</li>
<li>boots quicker than a traditional operating system which lends to cloud applications</li>
</ul>

<p>While all this might be true, I have some reservations about dynamically pulling pieces of the operating system to build your final application image. Unikernel System via their tools claim to have solved that dependency nightmare. For the sake of argument if we accept that have been able to solve dynamic dependencies between modules, a lot of these operating systems have been rewritten to the ground up which begs the questions about stability. Additionally, these unikernels don't have a traditional kernel or protected layer meaning that the application has full access all the way down the stack. Think about the security ramifications of this design.</p>

<p>It seems like unikernels are at odds with what Ubuntu Core decided to do which was just create a common minimal operating system using the same exact libraries we have been using for decades to run cloud and embedded applications. I think I prefer the Ubuntu Core design, but I have a feeling the hype and backing of Docker's marketing machine might smash that idea.</p>

<p><img src="https://raw.githubusercontent.com/dvonthenen/blog/master/images/dockerlameetup.jpg" alt="Jérôme Petazzoni of Docker, Amir Chaudhry and Richard Mortier of Unikernel Systems" /></p>

<h3>Mentoring in Open Source</h3>

<p>I attended the <a href="https://www.socallinuxexpo.org/scale/14x/presentations/dent-universe-mentoring-open-source">Dent the Universe: Mentoring in Open Source</a> session after a fellow coworker was interested in but was unable to attend SCaLE. I was pleasantly surprised with both the content of the discussion as well as the make up of the audience. The audience had a mix of gender, race, backgrounds, and also age... as in there were teens, toddlers, and babies in the audience (pretty sure the babies didn't really follow was was going on).</p>

<p>The upshot of the session is that relationships between mentors and mentees bring forth a sense of community like what we see in the open source world. Its a platform for exchanging ideas similar to a meetup but on an individual 1 on 1 setting. There are many open source projects and organizations out there that have mentoring programs included by not limited to: <a href="http://en.flossmanuals.net/GSoCMentoring/">Google</a>, <a href="https://community.apache.org/mentoringprogramme.html">Apache</a>, <a href="https://fedoraproject.org/wiki/Mentors">Fedora</a>, and <a href="http://wiki.ubuntu-women.org/Mentoring">Ubuntu</a>. The session covered what mentors are and aren't, how you build that relationship, the dedication that mentors need to have, and even how mentees might go about getting a mentor. Of all the sessions that I did sit in on, I have to say I took the most amount of notes in this one... maybe it was because the subject matter was so different, but nonetheless this was definitely an excellent session and was well worth the time.</p>

<h3>Nomad, a Golang Cluster Scheduler</h3>

<p>I just want to give a quick mention about the <a href="https://www.socallinuxexpo.org/scale/14x/presentations/nomad-introduction-cluster-schedulers">Nomad - An introduction to Cluster Schedulers</a> session. I found the overall design of Nomad intriguing based on the claimed feature set. <a href="https://www.nomadproject.io/">Nomad</a> is a <a href="https://www.hashicorp.com/">HashiCorp</a> project which if you don't know who HashiCorp is, they are the ones who brought you projects like <a href="https://www.vagrantup.com/">Vagrant</a> and <a href="https://www.consul.io/">Consul</a>. Nomad is written in Golang and supports scheduling for virtual infrastructure, Docker, Java applications, and etc. They have support for Global and Local cluster regions (think multi-datacenter support) and has facilities for deploying, scaling, rolling updates, load balancing, integrates with Consul and more. Although it seems like scale isn't their strong suit as the presenter only believes the stress testing has only been done on 100 or so nodes; however, Nomad might be a good enough solution for a mid-sized business. I will be keeping on eye on this considering the success that HashiCorp has had on their other projects.</p>

<p><img src="https://raw.githubusercontent.com/dvonthenen/blog/master/images/scalex14.jpg" alt="Expo Floor at SCaLE x14" /></p>

<h3>Conclusion</h3>

<p>Overall, SCaLE was a great conference with veritable buffet of topics that can interest a wide variety of people who attend. Hell for the price, under $40 if you get in on a coupon code, you can't beat the value for the level of information in the sessions and the discussions had with the many attendees and speakers. I would definitely recommend checking out this conference next year and if you can convince your employer to fund the trip or if you can't and have the spare time and (not much) cash to come on your own dime, its definitely a worth while experience.</p>
