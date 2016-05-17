---
post_title: 'EMC World 2016 Aftermath'
layout: post
published: true
---
Wow! How quickly a week can go by. Like many of you, [EMC World 2016](http://www.emcworld.com/index.htm) was my first time in attendance and it also happen to be the first time I have been given the opportunity to be a presenter for a larger audience. I though the experience exceeded my expectations and based on some of the preliminary numbers and feedback that we have been getting on the sessions the [EMC {code}](http://emccode.com/) team had presented, a good number of you agree the sessions content and presentations were of value to you. Thanks again for attending the sessions and providing your feedback.

### Couldn't make it this year?

For those that couldn't make it out this year, a number of people in {code} have starting posting the materials and slide decks for our sessions. Official EMC World slide decks should be posted in the coming weeks, but there have been a large number of requests to get a hold of the material ASAP and many of us on the team have been happy to oblige. As for my sessions, you can find the materials below.

#### Introduction To Mesos & Mesosphere

Here is the session material for [Introduction To Mesos & Mesosphere](https://www.emcworldonline.com/2016/connect/sessionDetail.ww?SESSION_ID=2714) (Monday May 2 at 8:30) which just as the title says is a [Apache Mesos](http://mesos.apache.org/) 101 type session.

You can download the "Introduction To Mesos & Mesosphere" powerpoint presentation [HERE](https://github.com/dvonthenen/proposals/raw/master/2016_EMCW/code.08%20Introduction%20to%20Mesos%20and%20Mesosphere.pptx). The video of the demonstration used at the end of the session highlighting Mesos using persistent external storage can be found on YouTube below:

<iframe width="420" height="315" src="https://www.youtube.com/embed/W353f2YVK9Y" frameborder="0" allowfullscreen></iframe>

The source code for the MVC Web Application written in Golang can be found in my [GitHub repo](https://github.com/dvonthenen/goprojects). The two projects used are [RestServer](https://github.com/dvonthenen/goprojects/tree/master/src/restserver) and [RestClient](https://github.com/dvonthenen/goprojects/tree/master/src/restclient).

To launch the MVC Application with external persistent storage, you first need to have [Mesos DNS](http://mesosphere.github.io/mesos-dns/) running and installed in your Mesos Cluster and then you can find the Marathon json files to launch tasks [here](https://github.com/dvonthenen/junkyard/tree/master/mesos/EMCW2016). To start up the application, perform the following:

<pre>
Start PostgreSQL:
curl -k -XPOST -d @postgres-mvc.json -H "Content-Type: application/json" YourMarathonIP:8080/v2/apps

Start RestServer:
curl -k -XPOST -d @restapi.json -H "Content-Type: application/json" YourMarathonIP:8080/v2/apps

Start RestClient:
curl -k -XPOST -d @ui.json -H "Content-Type: application/json" YourMarathonIP:8080/v2/apps
</pre>

#### Deep Dive With Mesos & Persistent Storage For Applications

Here is the session material for [Deep Dive With Mesos & Persistent Storage For Applications](https://www.emcworldonline.com/2016/connect/sessionDetail.ww?SESSION_ID=2720) (Tuesday May 3 at 3:00) which covered the importance of [Apache Mesos Frameworks](http://mesos.apache.org/documentation/latest/frameworks/) and the powerful capabilities that 2 layer scheduling provides in your Datacenter and Mesos cluster.

You can download the "Deep Dive With Mesos & Persistent Storage For Applications" powerpoint presentation [HERE](https://github.com/dvonthenen/proposals/raw/master/2016_EMCW/code.14%20Deep%20Dive%20with%20Mesos%20and%20Persistent%20Storage%20for%20Applications.pptx). The video of the demonstration used at the end of the session highlighting the Elastic Search Mesos Framework using persistent external storage can be found on YouTube below:

<iframe width="420" height="315" src="https://www.youtube.com/embed/UewRlc0ZWZ8" frameborder="0" allowfullscreen></iframe>

To launch the [Elastic Search Framework](https://github.com/mesos/elasticsearch) with external persistent storage, you first need to have at least a 3 Agent/Slave nodes in your Mesos cluster and then you can find the Marathon json files to launch tasks [here](https://github.com/dvonthenen/junkyard/tree/master/mesos/EMCW2016). To start up the application, perform the following:

<pre>
Start ElasticSearch Scheduler:
curl -k -XPOST -d @elasticsearch.json -H "Content-Type: application/json" YourMarathonIP:8080/v2/apps
</pre>

If you want to run some of the advanced ElasticSearch functionality used in the demo, you can find additional information in this file [here](https://github.com/dvonthenen/junkyard/blob/master/mesos/EMCW2016/start.txt).

### What's Next...

After recharging for a bit, we have already started on our post-EMC World plans and deliverables. Hopefully this will bring a forth a bunch of interesting ideas and projects for you guys to use. To keep up to date with the things that I will be working on, please follow me on Twitter at [@dvonthenen](https://twitter.com/dvonthenen). Any if anyone has any questions about the EMC World presentations, you can always catch me on the [{code} Community Slack](https://codecommunity.slack.com/) channel.
