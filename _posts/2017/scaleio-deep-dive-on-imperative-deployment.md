---
ID: 96
post_title: 'ScaleIO: Deep Dive on Imperative Deployment'
post_name: >
  scaleio-deep-dive-on-imperative-deployment
author: David vonThenen
post_date: 2017-01-18 08:24:33
layout: post
link: >
  https://dvonthenen.com/2017/01/18/scaleio-deep-dive-on-imperative-deployment/
published: true
tags:
  - Apache Mesos
  - Deep Dive
  - Framework
  - ScaleIO
  - ScaleIO Framework
  - Storage
categories:
  - Apache Mesos
---
<p>By now you probably have read the blog post, <a href="https://blog.codedellemc.com/2017/01/10/scaleio-framework-v03/">ScaleIO Framework v0.3: Deploy This!</a>, where we announced the new version of the ScaleIO Framework. (If you haven't, I would definitely go check it out first.) In that release, a new feature called Imperative Deployment was unveiled which is the first structured method for deploying ScaleIO into your <a href="http://mesos.apache.org/">Apache Mesos</a> cluster. In this blog post, we are going to do a deep dive for that feature and highlight some of the interesting and cool things that Imperative Deployment brings to this release.</p>

<h3>Let's Kick this Off</h3>

<p>The first thing we should point out when it comes to <a href="https://www.emc.com/storage/scaleio/index.htm">ScaleIO</a> is that you need a strategy when it comes to how you want to deploy it. Since ScaleIO is flexible and allows for infinite possible combinations, each one of those combinations has pros and cons. So it turns out that the marketing material that makes ScaleIO super easy to use glosses over the fact that there is actually a set of <a href="https://github.com/codedellemc/scaleio-framework/raw/master/.docs/user-guide/h15148-emc-scaleio-deployment-guide.pdf">best practices</a> that you need to adhere to get the most out of ScaleIO.</p>

<p>We are going to tackle various ScaleIO deployment scenarios in a series of installment blogs and our first topic will discuss environments for demos, dev/test, and smaller configurations. In this type of configuration, a fully distributed or hyper-converged deployment might be best to roll out since you are dealing with a relatively small number of systems. Demo and dev/test environments are trivial as it "just needs to work" and performance is an afterthought. So let's take a look at a real world hyper-converged configuration. It goes without saying that you want to have at least a 3-node Mesos master quorum to tolerate failure. For the ScaleIO MDM nodes (Primary, Secondary, and TieBreaker), we will make use of the 3 nodes used for the Mesos masters. Then for the compute, we will have 16 Mesos Agent nodes configured each with a single 2TB drive. This configuration must have already been pre-created prior to deploying the ScaleIO Framework.</p>

<p><img src="https://github.com/dvonthenen/blog/raw/master/images/MesosConfiguration.png" alt="Mesos Configuration" /></p>

<p>To deploy ScaleIO using the Framework's <a href="http://scaleio-framework.readthedocs.io/en/latest/user-guide/deployment-strategies/#imperative-deployment">Imperative Deployment feature</a>, you would define similar <a href="http://mesos.apache.org/documentation/latest/attributes-resources/">Mesos Agent attributes</a> as mentioned in the "Deploy This!" blog article. Before we begin, it is important to understand what the <code>scaleio-sds</code> and <code>scaleio-sdc</code> attributes really mean. The <code>scaleio-sds</code> represents the protection domains and storage pools that will be created on ScaleIO and which disks/devices will be contributed to that domain/pool combination. The <code>scaleio-sdc</code> represents the protection domains and storage pools to which that particular node will provision and consume ScaleIO volumes from. So very simply put, the difference between <code>sds</code> and <code>sdc</code> is <code>sds</code> is the server configuration of the disk/devices offered up to ScaleIO and <code>sdc</code> is the client configuration to consume volumes from ScaleIO.</p>

<h3>The Imperative Configuration</h3>

<p>So in the 3 Mesos Master + 3 ScaleIO MDMs and 16 Mesos Agent node configuration defined above, if you had the 2TB drives installed at <code>/dev/xvdf</code> for each node (can be verified using <code>fdisk</code>), your Mesos Agent node's attributes would look like the block below. Note that any changes to your Mesos Agent attributes will require a restart of your Mesos Agent service before deployment of the Framework.</p>

<pre># cat /etc/mesos-slave/attributes/scaleio-sds-domains
mydomain

#cat /etc/mesos-slave/attributes/scaleio-sds-mydomain
mypool

# cat /etc/mesos-slave/attributes/scaleio-sds-mypool
/dev/xvdf

# cat /etc/mesos-slave/attributes/scaleio-sdc-domains
mydomain

#cat /etc/mesos-slave/attributes/scaleio-sdc-mydomain
mypool
</pre>

<p>Now a few things should be noted. It might be wise to use more meaningful names than mydomain or mypool. If this was for the Quality Engineering department, maybe <code>mydomain</code> can be replaced with <code>engineering</code> and mypool with <code>qe</code>. The next thing is this assumes all devices are configured at <code>/dev/xvdf</code> but depending on your storage controller, the drive might be at <code>/dev/xvdg</code> for example, so replace it with the discovered or assigned value. Lastly, since <a href="https://github.com/codedellemc/rexray">REX-Ray</a> currently only supports provisioning volumes from ScaleIO on a single protection domain and storage pool, we could omit the definition of any <code>/etc/mesos-slave/attributes/scaleio-sdc</code> attributes. There exists code such that the last defined <code>scaleio-sds</code> domain and pool are automatically used for the <code>scaleio-sdc</code> components. When REX-Ray implements multi-domain/pool capabilities, this code will likely be deprecated.</p>

<p>Finally, let's assume that we know for certain that all the disks/devices are attached to <code>/dev/xvdf</code> because the initial setup was performed using your favorite DevOps tool or you are in AWS (<code>/dev/xvdf</code> happens default when you add your first disk), you could have deployed based on the ScaleIO Framework's <a href="http://scaleio-framework.readthedocs.io/en/latest/user-guide/deployment-strategies/#single-global-pool">Single Global Pool</a> method which would automatically attached all unused (ie without a filesystem) disks on the 16 Mesos Agent nodes. The default protection domain and default storage pool names can be overwritten with meaningful names using the <a href="http://scaleio-framework.readthedocs.io/en/latest/user-guide/configuration/#basic-command-line-options">configuration options</a> <code>-scaleio.protectiondomain=engineering</code> and <code>-scaleio.storagepool=qe</code>. The end results of both methods in this particular case would have been identical.</p>

<p><img src="https://github.com/dvonthenen/blog/raw/master/images/HugeMistake.jpg" alt="Huge Mistake" /></p>

<p>This appears to be simpler than the Imperative deployment, why don't we just use the Single Global Pool method all the time? First, keep in mind that only a single protection domain and single storage pool can be created. You may want to have more that one and in that case, you must use Imperative Deployment (Example Below). Second, if you have disks without a partition that you want to allocate for some other function like additional local storage, the Single Global Pool method will <em>automatically</em> consume and contribute that disk/device to ScaleIO. Warning: This includes Agent nodes to be added to the cluster for expansion! Defining these attributes for new nodes to be on-boarded to the cluster yields an explicit configuration and without these attributes, newly on-boarded nodes will contribute all disks/devices presented to that node based on the <code>-scaleio.protectiondomain</code> and <code>-scaleio.storagepool</code> configuration options.</p>

<p>An example of multiple StoragePools. Maybe Mesos Agent nodes 1-8 are defined like:</p>

<pre># cat /etc/mesos-slave/attributes/scaleio-sds-domains
engineering

#cat /etc/mesos-slave/attributes/scaleio-sds-engineering
qe

# cat /etc/mesos-slave/attributes/scaleio-sds-qe
/dev/xvdf
</pre>

<p>And Mesos Agent nodes 9-16 are defined like:</p>

<pre># cat /etc/mesos-slave/attributes/scaleio-sds-domains
engineering

#cat /etc/mesos-slave/attributes/scaleio-sds-engineering
development

# cat /etc/mesos-slave/attributes/scaleio-sds-development
/dev/xvdf
</pre>

<h3>What's next?</h3>

<p>A piece of functionality that is currently being worked on is Fault Sets. This will allow one to specify which nodes can fail without data loss. This will naturally allow for advanced configurations for ScaleIO and happens to be the target for the next blog article in this series.</p>

<p>Further down the road, there are plans to work on a Declarative Deployment option which kind of sits between the simplicity of the Single Global Pool and the explicit Imperative Deployment methods. By providing more abstract constructs, your end result will yield deployment of bigger configurations without getting into the weeds of managing what devices belong to what protection domain or storage pool.</p>

<p>Be sure to check out the <a href="https://github.com/codedellemc/scaleio-framework">ScaleIO Framework</a> project on GitHub and visit the <a href="https://github.com/codedellemc/labs/tree/master/setup-scaleio-aws-custom">{code} labs</a> page to test drive this feature. All feedback is welcome!</p>
