---
ID: 68
post_title: Looking Back at EMC World 2016
post_name: looking-back-at-emc-world-2016
author: david
post_date: 2016-05-17 10:19:57
layout: post
link: >
  https://dvonthenen.com/2016/05/17/looking-back-at-emc-world-2016/
published: true
tags:
  - Apache Mesos
  - Code
  - Conferences
  - Deep Dive
  - Elastic Search
  - 'EMC {code}'
  - EMC World
  - Golang
  - Mesos
  - mesos-module-dvdi
  - Mesosphere
  - Personal
  - Storage
  - Volume Driver
categories:
  - 'EMC {code}'
---
<p>Wow! How quickly a week can go by. Like many of you, <a href="http://www.emcworld.com/index.htm">EMC World 2016</a> was my first time in attendance and it also happen to be the first time I have been given the opportunity to be a presenter for a larger audience. I though the experience exceeded my expectations and based on some of the preliminary numbers and feedback that we have been getting on the sessions the <a href="http://emccode.com/">EMC {code}</a> team had presented, a good number of you agree the sessions content and presentations were of value to you. Thanks again for attending the sessions and providing your feedback.</p>

<h3>Couldn't make it this year?</h3>

<p>For those that couldn't make it out this year, a number of people in {code} have starting posting the materials and slide decks for our sessions. Official EMC World slide decks should be posted in the coming weeks, but there have been a large number of requests to get a hold of the material ASAP and many of us on the team have been happy to oblige. As for my sessions, you can find the materials below.</p>

<h4>Introduction To Mesos &amp; Mesosphere</h4>

<p>Here is the session material for <a href="https://www.emcworldonline.com/2016/connect/sessionDetail.ww?SESSION_ID=2714">Introduction To Mesos &amp; Mesosphere</a> (Monday May 2 at 8:30) which just as the title says is a <a href="http://mesos.apache.org/">Apache Mesos</a> 101 type session.</p>

<p>You can download the "Introduction To Mesos &amp; Mesosphere" powerpoint presentation <a href="https://github.com/dvonthenen/proposals/raw/master/2016_EMCW/code.08%20Introduction%20to%20Mesos%20and%20Mesosphere.pptx">HERE</a>. The video of the demonstration used at the end of the session highlighting Mesos using persistent external storage can be found on YouTube below:</p>

<iframe width="420" height="315" src="https://www.youtube.com/embed/W353f2YVK9Y" frameborder="0" allowfullscreen></iframe>

<p>The source code for the MVC Web Application written in Golang can be found in my <a href="https://github.com/dvonthenen/goprojects">GitHub repo</a>. The two projects used in demo were <a href="https://github.com/dvonthenen/goprojects/tree/master/src/restserver">RestServer</a> and <a href="https://github.com/dvonthenen/goprojects/tree/master/src/restclient">RestClient</a>.</p>

<p>To launch the MVC Application with external persistent storage, you first need to have each of your Mesos Agent/Slave nodes running <a href="http://mesosphere.github.io/mesos-dns/">Mesos DNS</a> and configured for persistent external storage using this <a href="http://dvonthenen.com/2016/03/08/mesos-module-dvdi-installation-walkthrough/">Guide</a>. Once you have those prerequisites in your Mesos Cluster, you can find the Marathon JSON files to launch tasks <a href="https://github.com/dvonthenen/junkyard/tree/master/mesos/EMCW2016">here</a>. To start up the application, perform the following:</p>

<pre>Start PostgreSQL:
curl -k -XPOST -d @postgres-mvc.json -H "Content-Type: application/json" YourMarathonIP:8080/v2/apps

Start RestServer:
curl -k -XPOST -d @restapi.json -H "Content-Type: application/json" YourMarathonIP:8080/v2/apps

Start RestClient:
curl -k -XPOST -d @ui.json -H "Content-Type: application/json" YourMarathonIP:8080/v2/apps
</pre>

<h4>Deep Dive With Mesos &amp; Persistent Storage For Applications</h4>

<p>Here is the session material for <a href="https://www.emcworldonline.com/2016/connect/sessionDetail.ww?SESSION_ID=2720">Deep Dive With Mesos &amp; Persistent Storage For Applications</a> (Tuesday May 3 at 3:00) which covered the importance of <a href="http://mesos.apache.org/documentation/latest/frameworks/">Apache Mesos Frameworks</a> and the powerful capabilities that 2 layer scheduling provides in your Datacenter and Mesos cluster.</p>

<p>You can download the "Deep Dive With Mesos &amp; Persistent Storage For Applications" powerpoint presentation <a href="https://github.com/dvonthenen/proposals/raw/master/2016_EMCW/code.14%20Deep%20Dive%20with%20Mesos%20and%20Persistent%20Storage%20for%20Applications.pptx">HERE</a>. The video of the demonstration used at the end of the session highlighting the Elastic Search Mesos Framework using persistent external storage can be found on YouTube below:</p>

<iframe width="420" height="315" src="https://www.youtube.com/embed/UewRlc0ZWZ8" frameborder="0" allowfullscreen></iframe>

<p>To launch the <a href="https://github.com/mesos/elasticsearch">Elastic Search Framework</a> with external persistent storage, you first need to have at least a 3 Agent/Slave nodes in your Mesos cluster and each of your Mesos Agent/Slave nodes configured for persistent external storage using this <a href="http://dvonthenen.com/2016/03/08/mesos-module-dvdi-installation-walkthrough/">Guide</a>. To start the ElasticSearch scheduler, you can find the Marathon JSON files to launch task <a href="https://github.com/dvonthenen/junkyard/tree/master/mesos/EMCW2016">here</a>. To start up the Scheduler, perform the following:</p>

<pre>Start ElasticSearch Scheduler:
curl -k -XPOST -d @elasticsearch.json -H "Content-Type: application/json" YourMarathonIP:8080/v2/apps
</pre>

<p>If you want to run some of the advanced ElasticSearch functionality used in the demo, you can find additional information in this file <a href="https://github.com/dvonthenen/junkyard/blob/master/mesos/EMCW2016/start.txt">here</a>.</p>

<h3>What's Next...</h3>

<p>After recharging for a bit, we have already started on our post-EMC World plans and deliverables. Hopefully this will bring a forth a bunch of interesting ideas and projects for the community. To keep up to date with the things that I will be working on, please follow me on Twitter at <a href="https://twitter.com/dvonthenen">@dvonthenen</a>. If anyone has any questions about the EMC World presentations, you can always catch me on the <a href="https://codecommunity.slack.com/">{code} Community Slack</a> channel.</p>
