---
ID: 45
post_title: Looking Back at SCaLE x14
author: dvonthenen
post_date: 2016-02-02 08:30:54
post_excerpt: ""
layout: post
permalink: >
  http://dvonthenen.com/2016/02/02/looking-back-at-scale-x14/
published: true
shorturl:
  - http://goo.gl/AyVBFU
---
For those that don't know what [SCaLE][1] is... SCaLE stands for the [Southern California Linux Expo][1] and this marked the 14th year the conference has been held which happened to be in Pasadena, CA on Jan 21-24. This was the first time I have attended SCaLE and I have to say that it was quite refreshing going to a conference where the primary purpose wasn't trying to sell you product but rather ideas and open source projects of interest. A lot of this is due to a very community driven focus which encompassed everything from session selection, a large volunteer staff, and etc.

There were a number of sessions and tracks ranging from Ubucon (everything Ubuntu), PostgreSQL, MySQL, Apache Bigtop, Open Source in Education, Unikernels, Robotics using Golang (pictured below) and etc. I took it upon myself to take a slice out of each pie to get a good feel for what the conference had to offer. Like everything in life, there was a range of the good, the bad and the ugly... but I would have to say there wasn't much, if any, in the latter category. For reference, you can get a copy of the conference schedule [here][2] as a sampling of what types of sessions SCaLE provides.

![Gobot - Robots Powered by Golang][3]

The other significant difference is that audience or the make up of the attendees in this conference is significantly different than traditional conferences backed by big money which in this instance was predominately developers, DevOps peeps, and sysadmins. Gone are the sales and marketing people with the heavy focus on landing deals with private rooms off to the side where contracts are being drawn up. If you are looking to network with other developers and the actual users of a particular technology, this is a fantastic place to connect with those people.

Without further ado, here are some notes from sessions I found interesting. It should also be noted that I have included a lot of the session links in this blog as many of the slide decks are available on those pages.

### IoT and Snappy Ubuntu Core

This session was titled [Internet of Things gets 'snappy' with Ubuntu Core][4] and its main focus was introducing people to the idea of [Snappy Ubuntu Core][5] which Ubuntu is pushing as the solution for building applications in the cloud and on embedded devices. Think of it as a minimal Ubuntu operating system built using the same libraries as traditional Ubuntu just with a much smaller footprint wrapped with a convenient package management that can orchestrate installation and upgrades. The IoT demo was an application built using snappy on a raspberry pi with an IP camera that can do facial tracking. Pretty cool! You can find more information about Snappy Ubuntu Core [here][6].

![IoT using Snappy Ubuntu Core on a Raspberry Pi][7]

### Juju Charms

This was my first exposure to Juju Charms. I had heard the name being thrown around before but didn't have the time to take a look at the offering. The session [Writing your first Juju Charm][8] was a well presented introduction for first time users. The main takeaway is that Juju models relationships with other applications encapsulated in what is called a charm and that those charms, for example MySQL, are created, maintained, and tuned by subject experts. Pulling those vetted charms helps provide a solid foundation to build your applications on. Dependencies in charms are automatically pulled in and the likeness was compared that to the layers of an onion; you just layer in the applications you want. Then when pulling together your solution, its simply dragging and connecting these charms within the UI.

In writing your own charm, the method of hooking these applications together is done by an event driven engine. For example, your application contained within your own charm doesn't get installed until the http server started and the MariaDB started event are received. Overall a good session. You can find more information about Juju [here][9] and [here][10].

### Docker and Unikernels

Although what I am going to write about here wasn't a session at SCaLE per se, but speakers from both the Docker and Unikernel presentations at this special edition of the [Docker LA Meetup SCaLE x14 Edition][11] had sessions at the conference. The first speaker was Jérôme Petazzoni from [Docker][12] (pictured on the right). We discussed Swarm and the advances made in the latest release specifically around mulit-network orchestration between containers instances to satisfy the cluster use case. I did enjoy the fact that the discussion was almost completely driven by demonstration, but from the sounds of known issues even walking into this demo, it makes me think that this isn't ready for prime time yet. I definitely appreciate the candor and honesty from the presenter about these issue; speaks to his professionalism.

The second half of the meetup quickly switched gears to talk about the Docker acquisition of [Unikernel Systems][13] who is the primary driver of the open source ecosystem of [unikernel.org][14]. Presenting a 101 type course on unikernels was Amir Chaudhry and Richard Mortier (pictured on the left). The session was quite fascinating considering I had not heard of the term unikernel until Docker made the announcement that morning. In a nutshell, the idea of a unikernel is such that you only take the various parts of a particular stack, say networking, that you need in order for your application to run. This implies that the stack is modular and has clear separation of concerns. The claim is that this type of operating system has:

*   a smaller footprint in terms of size
*   better security because they have a lower penetration profile
*   better performance because unnecessary services aren't running
*   boots quicker than a traditional operating system which lends to cloud applications

While all this might be true, I have some reservations about dynamically pulling pieces of the operating system to build your final application image. Unikernel System via their tools claim to have solved that dependency nightmare. For the sake of argument if we accept that have been able to solve dynamic dependencies between modules, a lot of these operating systems have been rewritten to the ground up which begs the questions about stability. Additionally, these unikernels don't have a traditional kernel or protected layer meaning that the application has full access all the way down the stack. Think about the security ramifications of this design.

It seems like unikernels are at odds with what Ubuntu Core decided to do which was just create a common minimal operating system using the same exact libraries we have been using for decades to run cloud and embedded applications. I think I prefer the Ubuntu Core design, but I have a feeling the hype and backing of Docker's marketing machine might smash that idea.

![Jérôme Petazzoni of Docker, Amir Chaudhry and Richard Mortier of Unikernel Systems][15]

### Mentoring in Open Source

I attended the [Dent the Universe: Mentoring in Open Source][16] session after a fellow coworker was interested in but was unable to attend SCaLE. I was pleasantly surprised with both the content of the discussion as well as the make up of the audience. The audience had a mix of gender, race, backgrounds, and also age... as in there were teens, toddlers, and babies in the audience (pretty sure the babies didn't really follow was was going on).

The upshot of the session is that relationships between mentors and mentees bring forth a sense of community like what we see in the open source world. Its a platform for exchanging ideas similar to a meetup but on an individual 1 on 1 setting. There are many open source projects and organizations out there that have mentoring programs included by not limited to: [Google][17], [Apache][18], [Fedora][19], and [Ubuntu][20]. The session covered what mentors are and aren't, how you build that relationship, the dedication that mentors need to have, and even how mentees might go about getting a mentor. Of all the sessions that I did sit in on, I have to say I took the most amount of notes in this one... maybe it was because the subject matter was so different, but nonetheless this was definitely an excellent session and was well worth the time.

### Nomad, a Golang Cluster Scheduler

I just want to give a quick mention about the [Nomad - An introduction to Cluster Schedulers][21] session. I found the overall design of Nomad intriguing based on the claimed feature set. [Nomad][22] is a [HashiCorp][23] project which if you don't know who HashiCorp is, they are the ones who brought you projects like [Vagrant][24] and [Consul][25]. Nomad is written in Golang and supports scheduling for virtual infrastructure, Docker, Java applications, and etc. They have support for Global and Local cluster regions (think multi-datacenter support) and has facilities for deploying, scaling, rolling updates, load balancing, integrates with Consul and more. Although it seems like scale isn't their strong suit as the presenter only believes the stress testing has only been done on 100 or so nodes; however, Nomad might be a good enough solution for a mid-sized business. I will be keeping on eye on this considering the success that HashiCorp has had on their other projects.

![Expo Floor at SCaLE x14][26]

### Conclusion

Overall, SCaLE was a great conference with veritable buffet of topics that can interest a wide variety of people who attend. Hell for the price, under $40 if you get in on a coupon code, you can't beat the value for the level of information in the sessions and the discussions had with the many attendees and speakers. I would definitely recommend checking out this conference next year and if you can convince your employer to fund the trip or if you can't and have the spare time and (not much) cash to come on your own dime, its definitely a worth while experience.

 [1]: https://www.socallinuxexpo.org/scale/14x
 [2]: https://www.socallinuxexpo.org/scale/14x/schedule/thursday
 [3]: https://raw.githubusercontent.com/dvonthenen/blog/master/images/gobot.jpg
 [4]: https://www.socallinuxexpo.org/scale/14x/presentations/internet-things-gets-snappy-ubuntu-core
 [5]: http://www.ubuntu.com/cloud/snappy
 [6]: https://developer.ubuntu.com/en/snappy/start/
 [7]: https://raw.githubusercontent.com/dvonthenen/blog/master/images/iot.jpg
 [8]: https://www.socallinuxexpo.org/scale/14x/presentations/writing-your-first-juju-charm
 [9]: https://jujucharms.com/about
 [10]: https://jujucharms.com/store?type=charm
 [11]: http://www.meetup.com/Docker-Los-Angeles/events/228120991/
 [12]: http://www.docker.com/
 [13]: http://unikernel.com/
 [14]: http://unikernel.org/
 [15]: https://raw.githubusercontent.com/dvonthenen/blog/master/images/dockerlameetup.jpg
 [16]: https://www.socallinuxexpo.org/scale/14x/presentations/dent-universe-mentoring-open-source
 [17]: http://en.flossmanuals.net/GSoCMentoring/
 [18]: https://community.apache.org/mentoringprogramme.html
 [19]: https://fedoraproject.org/wiki/Mentors
 [20]: http://wiki.ubuntu-women.org/Mentoring
 [21]: https://www.socallinuxexpo.org/scale/14x/presentations/nomad-introduction-cluster-schedulers
 [22]: https://www.nomadproject.io/
 [23]: https://www.hashicorp.com/
 [24]: https://www.vagrantup.com/
 [25]: https://www.consul.io/
 [26]: https://raw.githubusercontent.com/dvonthenen/blog/master/images/scalex14.jpg