---
ID: 40
post_title: >
  Difficulties with Multi-Version Mesos
  Support
post_name: >
  difficulties-with-multi-version-mesos-support
author: david
post_date: 2016-01-19 09:11:42
layout: post
link: >
  https://dvonthenen.com/2016/01/19/difficulties-with-multi-version-mesos-support/
published: true
tags:
  - Apache Mesos
  - C++
  - Code
  - Deep Dive
  - DLL Hell
  - Mesos
  - Mesosphere
categories:
  - Apache Mesos
---
<p>I have been working with <a href="http://mesos.apache.org/">Apache Mesos</a> for some time now and after a recent meeting with the <a href="https://mesosphere.com/">Mesosphere</a> team (the company largely driving the roadmap and development effort), I have come to learn that some Mesos users sit on the bleeding edge of releases and others that are sensitive to things like security, change management, and stability are more inclined to be a couple or several versions behind. This practice isn’t anything new, it took <a href="http://www.emc.com/">EMC</a> IT a long time to adopt Windows 7… heck, Windows 10 is out now and we are still sitting at Windows 7. Not saying that is necessarily a bad thing..</p>

<p><img src="https://raw.githubusercontent.com/dvonthenen/blog/master/images/windows10install.png" alt="Why you wait for the first Service Pack" /></p>

<p>So we have to accept the fact that we need to support multiple versions and take that into consideration when building components for Mesos. This takes us to the most recent release (0.4.0) of <a href="https://github.com/emccode/mesos-module-dvdi/releases/tag/v0.4.0">mesos-module-dvdi</a> which supports multiple versions spanning from 0.23.1 to the most recent release of mesos, 0.26.0. A side note, for those not familiar with mesos-module-dvdi its a Mesos isolator module that can create and mount external storage volumes, such as ScaleIO, Amazon EBS, or etc, and provide persistent storage for Applications created on Mesos. If you are interested, you can find more information on its <a href="https://github.com/emccode/mesos-module-dvdi">GitHub</a> page.</p>

<h3>Where’s the Beef?</h3>

<p>The mesos-module-dvdi is a C++11 project and as such needs to implement defined Mesos interfaces so that the module can be loaded and invoked from a Mesos slave (think of it as a simple node that launches tasks). If you take a look at the Isolator interface between versions, you will immediately notice that the interface varies wildly between versions and that modules created for one version will not work on another version of Mesos. Heck, even the interface class name you need to implement has changed:</p>

<p>In <a href="https://github.com/apache/mesos/blob/0.23.1/include/mesos/slave/isolator.hpp">0&#46;23.1</a>:<br />
<code>class IsolatorProcess : public process::Process</code></p>

<p><code>process::Future&lt;Nothing&gt; recover(
const std::list&lt;ExecutorRunState&gt;&amp; states,
const hashset&lt;ContainerID&gt;&amp; orphans);</code></p>

<p><code>process::Future&lt;Option&lt;CommandInfo&gt;&gt; prepare(
const ContainerID&amp; containerId,
const ExecutorInfo&amp; executorInfo,
const std::string&amp; directory,
const Option&lt;std::string&gt;&amp; rootfs,
const Option&lt;std::string&gt;&amp; user);</code></p>

<p>In <a href="https://github.com/apache/mesos/blob/0.24.1/include/mesos/slave/isolator.hpp">0&#46;24.1</a>:<br />
<code>class DockerVolumeDriverIsolator: public mesos::slave::Isolator</code></p>

<p><code>virtual process::Future&lt;Nothing&gt; recover(
const std::list&lt;ContainerState&gt;&amp; states,
const hashset&lt;ContainerID&gt;&amp; orphans);</code></p>

<p><code>virtual process::Future&lt;Option&lt;ContainerPrepareInfo&gt;&gt; prepare(
const ContainerID&amp; containerId,
const ExecutorInfo&amp; executorInfo,
const std::string&amp; directory,
const Option&lt;std::string&gt;&amp; user);</code></p>

<p>This is not good. This is basically Mesos’ version of Microsoft’s DLL Hell. As an experienced C++ developer, defining a C++ interface that is going to ensure backwards compatibility is very difficult. In previous projects I have worked on, the interfaces not only needed to be backwards compatible but also cross-platform (yes, Windows support) all from a single code base. That adds further complexity. These days some of these difficulties can easily be side stepped by standing up a REST endpoint. Even the worst REST APIs by in large “get the job done” even if the API is horribly designed. For example, some might argue if the API does not follow CRUD, HATEOAS, or etc they are bad designs, but I digress.</p>

<p><img src="https://raw.githubusercontent.com/dvonthenen/blog/master/images/dllhell.jpg" alt="DLL Hell" /></p>

<h3>A Meh Solution</h3>

<p>So the current solution to support multiple versions of the Mesos Isolator interface from a single code base has been to use ifdefs. Yea, unfortunately the solution isn’t as elegant as it could be, but with the break in backwards compatibility in the Isolator interface, it leaves us with very little choice. This is also compounded by the fact that this isn’t the only interface that is changing between versions. There are other Mesos support libraries in which the interface is in flux and adding an abstraction layer among all these functions, classes, and structures might take you down a rabbit hole that will leave you rocking yourself in the fetal position for months crying “so many functions”.</p>

<p>Since we are using ifdefs, that unfortunately means our solution is a compile time solution and we therefore need to build every version that we plan on supporting. We do that in our Makefile as see below.</p>

<p>In the <a href="https://github.com/emccode/mesos-module-dvdi/blob/v0.4.0/Makefile">Makefile</a> on lines 8 and 818 respectively:<br />
<code>ISO_VERSIONS := 0.23.1 0.24.1 0.25.0 0.26.0</code></p>

<p><code>$(foreach V,$(ISO_VERSIONS),$(eval $(call ISOLATOR_BUILD_RULES,$(V))))</code></p>

<p>Then we take that version and create an integer representation of the version by stripping out the periods from the version we are compiling. Represented by the <code>MESOS_VERSION_INT</code> preprocessor directive below. An example of that would be version 0.24.1 becomes 0241.</p>

<p>In the <a href="https://github.com/emccode/mesos-module-dvdi/blob/v0.4.0/Makefile">Makefile</a> lines 790-795:<br />
<code>$$(ISO_MAKEFILE_$1): CXXFLAGS=-I$$(GLOG_OPT_DIR)/include
-I$$(PICOJSON_OPT_DIR)/include
-I$$(BOOST_OPT_DIR)
-I$$(PBUF_OPT_DIR)/include
-DMESOS_VERSION_INT=$$(subst .,,$1)
$$(ISO_MAKEFILE_$1): $$(ISO_CONFIGURE_$1) $$(ISO_DEPS_$1)</code></p>

<p>Then we compile the project using the <code>MESOS_VERSION_INT</code> and make alterations to the code to handle the ifdefs.</p>

<p>In the <a href="https://github.com/emccode/mesos-module-dvdi/blob/v0.4.0/isolator/isolator/docker_volume_driver_isolator.hpp">docker_volume_driver_isolator.hpp</a>:<br />
<code>if MESOS_VERSION_INT != 0 &amp;&amp; MESOS_VERSION_INT &lt; 0240&lt;br /&gt;
class DockerVolumeDriverIsolator: public mesos::slave::IsolatorProcess&lt;br /&gt;
else&lt;br /&gt;
class DockerVolumeDriverIsolator: public mesos::slave::Isolator&lt;br /&gt;
endif</code></p>

<p><code>if MESOS_VERSION_INT != 0 &amp;&amp; MESOS_VERSION_INT &lt; 0240&lt;br /&gt;
process::Future&lt;Nothing&gt; recover(
const std::list&lt;ExecutorRunState&gt;&amp; states,
const hashset&lt;ContainerID&gt;&amp; orphans);&lt;br /&gt;
else&lt;br /&gt;
virtual process::Future&lt;Nothing&gt; recover(
const std::list&lt;ContainerState&gt;&amp; states,
const hashset&lt;ContainerID&gt;&amp; orphans);&lt;br /&gt;
endif</code></p>

<p><code>if MESOS_VERSION_INT != 0 &amp;&amp; MESOS_VERSION_INT &lt; 0240&lt;br /&gt;
process::Future&lt;Option&lt;CommandInfo&gt;&gt; prepare(
const ContainerID&amp; containerId,
const ExecutorInfo&amp; executorInfo,
const std::string&amp; directory,
const Option&lt;std::string&gt;&amp; rootfs,
const Option&lt;std::string&gt;&amp; user);&lt;br /&gt;
else&lt;br /&gt;
process::Future&lt;Option&lt;ContainerPrepareInfo&gt;&gt; prepare(
const ContainerID&amp; containerId,
const ExecutorInfo&amp; executorInfo,
const std::string&amp; directory,
const Option&lt;std::string&gt;&amp; user);&lt;br /&gt;
endif</code></p>

<p>So that is it. Again, not the most elegant solution but unfortunately addressing the backwards compatibility is going to be very difficult to resolve going forward as it would require retrofitting older versions of Mesos. That could get pretty ugly to change architecture in the form of patches or even a minor release. Odds are this is not going to go away any time soon. I hope this helps other developers out there looking to create Mesos Isolators (and Mesos Frameworks because it looks like the Framework interfaces may have the same problem).</p>
