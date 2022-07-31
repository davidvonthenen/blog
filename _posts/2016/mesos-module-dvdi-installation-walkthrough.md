---
ID: 56
post_title: >
  mesos-module-dvdi Installation
  Walkthrough
post_name: >
  mesos-module-dvdi-installation-walkthrough
author: david
post_date: 2016-03-08 07:55:57
layout: post
link: >
  https://dvonthenen.com/2016/03/08/mesos-module-dvdi-installation-walkthrough/
published: true
tags:
  - Apache Mesos
  - DVDI
  - External Storage
  - Mesos
  - mesos-module-dvdi
  - Storage
categories:
  - Apache Mesos
---
<p>As other people in the <a href="http://emccode.github.io/">EMC {code}</a> team started getting involved with <a href="http://mesos.apache.org/">Apache Mesos</a>, I have been helping by fielding questions and troubleshooting installations of both Mesos and <a href="https://github.com/emccode/mesos-module-dvdi">mesos-module-dvdi</a> so that others can kick the tires and become subject matter experts. It was brought to my attention that a good clean walkthrough of adding external volume/storage support to an existing <strong>production</strong> Mesos cluster might be of value to others which brings us to this blog post. We will cover a couple examples of launching <a href="https://mesosphere.github.io/marathon/">Marathon</a> tasks to make use of our new found capability. Also, we will take a look at functionality that the mesos-module-dvdi brings to the table and talk about what those features mean to you.</p>

<h3>Preflight Checklist</h3>

<p>Before we begin, I want to make sure that everyone who is going to follow along has a properly configured Mesos cluster and for those that don't have a Mesos cluster, there is a very good <a href="https://www.digitalocean.com/community/tutorials/how-to-configure-a-production-ready-mesosphere-cluster-on-ubuntu-14-04">walkthrough guide</a> by <a href="https://www.digitalocean.com/">DigitalOcean</a> that I would highly recommend. I would encourage performing every step in this guide and not skip or take short cuts when deploying your cluster as it can lead to wonky behavior of your cluster.</p>

<p><img src="https://raw.githubusercontent.com/dvonthenen/blog/master/images/wonky.jpg" alt="Wonky Mesos Clusters" /></p>

<p>There are a number of open source projects out there that attempt to deploy a proper Mesos cluster, but each one that I have evaluated (there have been a lot) has always fallen short somewhere. - <a href="https://github.com/everpeace/vagrant-mesos">everpeace/vagrant-mesos</a> currently only works for version 0.23.1 and older. There haven't been updates to the project in some time. - <a href="https://github.com/mesosphere/playa-mesos">mesosphere/playa-mesos</a> runs on virtualbox, but I found a number of issues with how the nodes are configured and ran into issues using certain frameworks, such as the <a href="https://github.com/mesos/elasticsearch">Elastic Search</a> framework, because of virtualbox's networking layer. The configuration that is deployed is only a single master node; so no high availability. This project also hasn't been updated in some time. - <a href="https://minimesos.org/">minimesos</a> project deploys a configuration that is backed by docker containers so if you need to modify something like the slave nodes (in our case, we need to) this isn't going to be a good option. Like the previous method, single master and no high availability.</p>

<p>If you want kick the tires on all the functionality that Mesos has to offer, I would still recommend that you deploy these nodes by hand. If that is too time consuming or you don't have access to AWS or hardware resources, <code>playa-mesos</code> gets you most of the way there. The {code} team has a fork of that project which has some bug fixes as well as some enhancements. You can find our fork on <a href="https://github.com/">GitHub</a> located at <a href="https://github.com/emccode/vagrant/tree/master/playa-mesos">emccode/vagrant/playa-mesos</a> and if you chose to go this route, the {code} fork of playa-mesos has <code>mesos-module-dvdi</code> already pre-configured using VirtualBox as the external storage provider in which case you can use this walkthough to verify your settings. Just a reminder, the purpose of this walkthrough is adding external persistent volumes for <strong>production</strong> Mesos environments in which you will most likely take the steps below and incorporate them into a DevOps tool that your organization uses.</p>

<h3>mesos-module-dvdi Installation Walkthrough</h3>

<p><code>mesos-module-dvdi</code> builds on top of functionality that is provided by <code>REX-Ray</code> and <code>DVDCLI</code>. So the first thing we need to do is install both packages on all your Mesos slave/agent nodes (<strong>NOTE:</strong> that all steps outlined in this section needs to be done on all your slave/agent nodes as root). You can do that by running the following commands:</p>

<pre>curl -sSL https://dl.bintray.com/emccode/rexray/install | sh -
curl -sSL https://dl.bintray.com/emccode/dvdcli/install | sh -
</pre>

<p>Then you need you configure <a href="https://github.com/emccode/rexray">REX-Ray</a> with the storage provider you plan on using. Since I usually host my configurations on Amazon for demonstration purposes, I am going to configure <code>REX-Ray</code> to use EC2 and place the file at /etc/rexray/config.yml. This is what my configuration file looks like:</p>

<pre>rexray:
  storageDrivers:
  - ec2
  volume:
    mount:
      preempt: true
    unmount:
      ignoreUsedCount: true
aws:
  accessKey: &lt;YOUR_ACCESS_KEY&gt;
  secretKey: &lt;YOUR_SECRET_KEY&gt;
</pre>

<p>You can do a simple test to make sure <code>REX-Ray</code> and <code>DVDCLI</code> are functioning properly by running the following command: <code>rexray volume</code>. If you need help on configuring REX-Ray for other storage platforms, you can view the <a href="http://rexray.readthedocs.org/en/stable/user-guide/config/">REX-Ray configuration guide</a>.</p>

<p>Next, we need to install the <code>mesos-module-dvdi</code> which gives us the ability to provision, mount, unmount external volumes to Mesos tasks. You can download the version that matches your Mesos configuration from our GitHub <a href="https://github.com/emccode/mesos-module-dvdi/releases">releases page</a>. My Mesos cluster is running version 0.27.0, so I am going to download libmesos_dvdi_isolator-0.27.0.so and place the .so in the /usr/lib directory. We also need to create a Mesos isolator configuration file so that the Mesos slave/agent knows how to load the isolator module. Create the file /usr/lib/dvdi-mod.json with the following content inside (replace with your version of the downloaded isolator):</p>

<pre>{
   "libraries": [
     {
       "file": "/usr/lib/libmesos_dvdi_isolator-0.27.0.so",
       "modules": [
         {
           "name": "com_emccode_mesos_DockerVolumeDriverIsolator",
           "parameters": [
            {
              "key": "isolator_command",
              "value": "/emc/dvdi_isolator"
            }
          ]
         }
       ]
     }
   ]
 }
</pre>

<p>Finally, we just need to add the hooks for the Mesos slave/agent to pick up the configuration file and load the isolator. You can do that by running the following shell commands:</p>

<pre>echo "docker,mesos" | tee /etc/mesos-slave/containerizers
echo "com_emccode_mesos_DockerVolumeDriverIsolator" | tee /etc/mesos-slave/isolation
echo "file:///usr/lib/dvdi-mod.json" | tee /etc/mesos-slave/modules
service mesos-slave restart
</pre>

<p>Success! This configuration will also allow you to run external volumes for docker workloads which is why you see docker in the first command. To learn more about using external storage with the docker containerizer in Apache Mesos, please checkout this great <a href="http://blog.emccode.com/2015/09/29/upcoming-docker-1-9-and-enhanced-storage-features-with-volume-options/">article</a> on the EMC {code} blog.</p>

<p><img src="https://raw.githubusercontent.com/dvonthenen/blog/master/images/greatsuccess.jpg" alt="Very Nice Great Success" /></p>

<h3>Exercising the mesos-module-dvdi</h3>

<p>Now that we have all this configured correctly, lets start out by creating a couple of external volumes by launching a Mesos task. Create a file called basic.json with the following content inside:</p>

<pre>{
  "id": "hello-mesos",
  "cmd": "while [ true ] ; do touch /var/lib/rexray/volumes/firstvolume/hello1 ; sleep 5 ; done",
  "mem": 16,
  "cpus": 0.1,
  "instances": 1,
  "env": {
    "DVDI_VOLUME_NAME": "firstvolume",
    "DVDI_VOLUME_OPTS": "size=5,iops=150,volumetype=io1,newfstype=xfs,overwritefs=false",
    "DVDI_VOLUME_DRIVER": "rexray",
    "DVDI_VOLUME_NAME1": "secondvolume",
    "DVDI_VOLUME_DRIVER1": "rexray",
    "DVDI_VOLUME_OPTS1": "size=6,volumetype=gp2,newfstype=ext4,overwritefs=false"
  }
}
</pre>

<p>Then we can launch the Marathon task by running the following shell command:</p>

<pre>curl -k -XPOST -d @basic.json -H "Content-Type: application/json" :8080/v2/apps
</pre>

<p>This simply creates two new volumes called "firstvolume" and "secondvolume" and uses the default <code>REX-Ray</code> mount location /var/librexray/volumes to mount these volumes.</p>

<p>For a more complex example, lets stand up a simple web server with an external volume and scribble some data on it. Create a file called web.json with the following content inside and replace YOUR_NAME_HERE with your first name (be careful and try not to remove the single quote trailing it):</p>

<pre>{
  "id": "webserver",
  "uris": [
    "https://github.com/dvonthenen/goprojects/raw/master/bin/statichttpserver"
  ],
  "cmd": "echo 'Hello, YOUR_NAME_HERE' $(cat /var/lib/rexray/volumes/webdata/readme.txt | wc -l) &gt;&gt; /var/lib/rexray/volumes/webdata/readme.txt && chmod u+x statichttpserver &&  ./statichttpserver -port=$PORT -path=/var/lib/rexray/volumes/webdata",
  "mem": 16,
  "cpus": 0.1,
  "instances": 1,
  "constraints": [
    ["hostname", "UNIQUE"]
  ],
  "env": {
    "DVDI_VOLUME_NAME": "webdata",
    "DVDI_VOLUME_OPTS": "size=5,iops=150,volumetype=io1,newfstype=xfs,overwritefs=false",
    "DVDI_VOLUME_DRIVER": "rexray"
  }
}
</pre>

<p>Then we can launch the Marathon task by running the following shell command:</p>

<pre>curl -k -XPOST -d @web.json -H "Content-Type: application/json" :8080/v2/apps
</pre>

<p>You can find out what slave/agent node and port the web server is running on by clicking on the running task in the Marathon UI and looking at the application configuration information on the page (as seen below).</p>

<p><img src="https://raw.githubusercontent.com/dvonthenen/blog/master/images/marathon.png" alt="Marathon Task" /></p>

<p>And if you open up that hostname and port in your web browser (you may have noticed that my FQDN name isn't the same since I configured my EC2 instances without elastic IPs), you will have accessed the webserver we downloaded from my <a href="https://github.com/dvonthenen/goprojects">GitHub account</a> and you should see a file readme.txt at the root. When you click that readme.txt to display the contents, you should see your name inside the text file.</p>

<p><img src="https://raw.githubusercontent.com/dvonthenen/blog/master/images/webpage.png" alt="Opening up the readme.txt" /></p>

<p>Although the second example is a fairly meaningful real world example of using external volumes/storage, the real benefits don't become apparent until you start doing things like failing the Mesos slave/agent node that is currently running the task. We can simulate this failure, by simply stopping the Marathon task and relaunching it. The new instance of our task may start up on a different node, but the important take away is that the external volume from the first instance will be reattached to the new task thus preserving any prior data. If you open the readme.txt after the task enters the running state, you should see the previous "Hello" line with a "0" at end now followed by a "Hello" line ending in "1" that this new instance has added on. Now just imagine a database like PostgreSQL or an Elastic Search node as a Marathon task. Any failure in the Mesos slave/agent or health check will trigger the task to start up from its previous state on a new node. Say "Hello" to high availability!</p>

<p><img src="https://raw.githubusercontent.com/dvonthenen/blog/master/images/persistence.png" alt="Persistence!" /></p>

<h3>What's Next...</h3>

<p>Very cool! Hopefully this served as a good guide to adding external volume/storage support to your existing Apache Mesos cluster using <code>REX-Ray</code>, <code>DVDCLI</code>, and <code>mesos-module-dvdi</code>. I have a couple of ideas on some follow up blog posts... thinking about how I do dev/test for <code>mesos-module-dvdi</code> with Docker containers or perhaps more about frameworks. If you have any topics you might want me to discuss, please drop me a line on twitter at <a href="https://twitter.com/dvonthenen">@dvonthenen</a>.</p>
