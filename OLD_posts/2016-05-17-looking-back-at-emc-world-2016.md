---
ID: 68
post_title: Looking Back at EMC World 2016
author: dvonthenen
post_date: 2016-05-17 10:19:57
post_excerpt: ""
layout: post
permalink: >
  http://dvonthenen.com/2016/05/17/looking-back-at-emc-world-2016/
published: true
shorturl:
  - http://goo.gl/rkcR9u
---
Wow! How quickly a week can go by. Like many of you, [EMC World 2016][1] was my first time in attendance and it also happen to be the first time I have been given the opportunity to be a presenter for a larger audience. I though the experience exceeded my expectations and based on some of the preliminary numbers and feedback that we have been getting on the sessions the [EMC {code}][2] team had presented, a good number of you agree the sessions content and presentations were of value to you. Thanks again for attending the sessions and providing your feedback.

### Couldn't make it this year?

For those that couldn't make it out this year, a number of people in {code} have starting posting the materials and slide decks for our sessions. Official EMC World slide decks should be posted in the coming weeks, but there have been a large number of requests to get a hold of the material ASAP and many of us on the team have been happy to oblige. As for my sessions, you can find the materials below.

#### Introduction To Mesos & Mesosphere

Here is the session material for [Introduction To Mesos & Mesosphere][3] (Monday May 2 at 8:30) which just as the title says is a [Apache Mesos][4] 101 type session.

You can download the "Introduction To Mesos & Mesosphere" powerpoint presentation [HERE][5]. The video of the demonstration used at the end of the session highlighting Mesos using persistent external storage can be found on YouTube below:

<iframe width="420" height="315" src="https://www.youtube.com/embed/W353f2YVK9Y" frameborder="0" allowfullscreen></iframe> 
The source code for the MVC Web Application written in Golang can be found in my [GitHub repo][6]. The two projects used in demo were [RestServer][7] and [RestClient][8].

To launch the MVC Application with external persistent storage, you first need to have each of your Mesos Agent/Slave nodes running [Mesos DNS][9] and configured for persistent external storage using this [Guide][10]. Once you have those prerequisites in your Mesos Cluster, you can find the Marathon JSON files to launch tasks [here][11]. To start up the application, perform the following:

<pre>Start PostgreSQL:
curl -k -XPOST -d @postgres-mvc.json -H "Content-Type: application/json" YourMarathonIP:8080/v2/apps

Start RestServer:
curl -k -XPOST -d @restapi.json -H "Content-Type: application/json" YourMarathonIP:8080/v2/apps

Start RestClient:
curl -k -XPOST -d @ui.json -H "Content-Type: application/json" YourMarathonIP:8080/v2/apps
</pre>

#### Deep Dive With Mesos & Persistent Storage For Applications

Here is the session material for [Deep Dive With Mesos & Persistent Storage For Applications][12] (Tuesday May 3 at 3:00) which covered the importance of [Apache Mesos Frameworks][13] and the powerful capabilities that 2 layer scheduling provides in your Datacenter and Mesos cluster.

You can download the "Deep Dive With Mesos & Persistent Storage For Applications" powerpoint presentation [HERE][14]. The video of the demonstration used at the end of the session highlighting the Elastic Search Mesos Framework using persistent external storage can be found on YouTube below:

<iframe width="420" height="315" src="https://www.youtube.com/embed/UewRlc0ZWZ8" frameborder="0" allowfullscreen></iframe> 
To launch the [Elastic Search Framework][15] with external persistent storage, you first need to have at least a 3 Agent/Slave nodes in your Mesos cluster and each of your Mesos Agent/Slave nodes configured for persistent external storage using this [Guide][10]. To start the ElasticSearch scheduler, you can find the Marathon JSON files to launch task [here][11]. To start up the Scheduler, perform the following:

<pre>Start ElasticSearch Scheduler:
curl -k -XPOST -d @elasticsearch.json -H "Content-Type: application/json" YourMarathonIP:8080/v2/apps
</pre>

If you want to run some of the advanced ElasticSearch functionality used in the demo, you can find additional information in this file [here][16].

### What's Next...

After recharging for a bit, we have already started on our post-EMC World plans and deliverables. Hopefully this will bring a forth a bunch of interesting ideas and projects for the community. To keep up to date with the things that I will be working on, please follow me on Twitter at [@dvonthenen][17]. If anyone has any questions about the EMC World presentations, you can always catch me on the [{code} Community Slack][18] channel.

 [1]: http://www.emcworld.com/index.htm
 [2]: http://emccode.com/
 [3]: https://www.emcworldonline.com/2016/connect/sessionDetail.ww?SESSION_ID=2714
 [4]: http://mesos.apache.org/
 [5]: https://github.com/dvonthenen/proposals/raw/master/2016_EMCW/code.08%20Introduction%20to%20Mesos%20and%20Mesosphere.pptx
 [6]: https://github.com/dvonthenen/goprojects
 [7]: https://github.com/dvonthenen/goprojects/tree/master/src/restserver
 [8]: https://github.com/dvonthenen/goprojects/tree/master/src/restclient
 [9]: http://mesosphere.github.io/mesos-dns/
 [10]: http://dvonthenen.com/2016/03/08/mesos-module-dvdi-installation-walkthrough/
 [11]: https://github.com/dvonthenen/junkyard/tree/master/mesos/EMCW2016
 [12]: https://www.emcworldonline.com/2016/connect/sessionDetail.ww?SESSION_ID=2720
 [13]: http://mesos.apache.org/documentation/latest/frameworks/
 [14]: https://github.com/dvonthenen/proposals/raw/master/2016_EMCW/code.14%20Deep%20Dive%20with%20Mesos%20and%20Persistent%20Storage%20for%20Applications.pptx
 [15]: https://github.com/mesos/elasticsearch
 [16]: https://github.com/dvonthenen/junkyard/blob/master/mesos/EMCW2016/start.txt
 [17]: https://twitter.com/dvonthenen
 [18]: https://codecommunity.slack.com/