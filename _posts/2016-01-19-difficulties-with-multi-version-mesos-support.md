---
ID: 40
post_title: >
  Difficulties with Multi-Version Mesos
  Support
author: dvonthenen
post_date: 2016-01-19 09:11:42
post_excerpt: ""
layout: post
permalink: >
  http://dvonthenen.com/2016/01/19/difficulties-with-multi-version-mesos-support/
published: true
---
I have been working with [Apache Mesos][1] for some time now and after a recent meeting with the [Mesosphere][2] team (the company largely driving the roadmap and development effort), I have come to learn that some Mesos users sit on the bleeding edge of releases and others that are sensitive to things like security, change management, and stability are more inclined to be a couple or several versions behind. This practice isn’t anything new, it took [EMC][3] IT a long time to adopt Windows 7… heck, Windows 10 is out now and we are still sitting at Windows 7. Not saying that is necessarily a bad thing..

![Why you wait for the first Service Pack][4]

So we have to accept the fact that we need to support multiple versions and take that into consideration when building components for Mesos. This takes us to the most recent release (0.4.0) of [mesos-module-dvdi][5] which supports multiple versions spanning from 0.23.1 to the most recent release of mesos, 0.26.0. A side note, for those not familiar with mesos-modue-dvdi its a Mesos isolator module that can create and mount external storage volumes, such as ScaleIO, Amazon EBS, or etc, and provide persistent storage for Applications created on Mesos. If you are interested, you can find more information on its [GitHub][6] page.

### Where’s the Beef?

The mesos-module-dvdi is a C++11 project and as such needs to implement defined Mesos interfaces so that the module can be loaded and invoked from a Mesos slave (think of it as a simple node that launches tasks). If you take a look at the Isolator interface between versions, you will immediately notice that the interface varies wildly between versions and that modules created for one version will not work on another version of Mesos. Heck, even the interface class name you need to implement has changed:

In [0\.23.1][7]:  
`class IsolatorProcess : public process::Process`

`process::Future<Nothing> recover(
const std::list<ExecutorRunState>& states,
const hashset<ContainerID>& orphans);`

`process::Future<Option<CommandInfo>> prepare(
const ContainerID& containerId,
const ExecutorInfo& executorInfo,
const std::string& directory,
const Option<std::string>& rootfs,
const Option<std::string>& user);`

In [0\.24.1][8]:  
`class DockerVolumeDriverIsolator: public mesos::slave::Isolator`

`virtual process::Future<Nothing> recover(
const std::list<ContainerState>& states,
const hashset<ContainerID>& orphans);`

`virtual process::Future<Option<ContainerPrepareInfo>> prepare(
const ContainerID& containerId,
const ExecutorInfo& executorInfo,
const std::string& directory,
const Option<std::string>& user);`

This is not good. This is basically Mesos’ version of Microsoft’s DLL Hell. As an experienced C++ developer, defining a C++ interface that is going to ensure backwards compatibility is very difficult. In previous projects I have worked on, the interfaces not only needed to be backwards compatible but also cross-platform (yes, Windows support) all from a single code base. That adds further complexity. These days some of these difficulties can easily be side stepped by standing up a REST endpoint. Even the worst REST APIs by enlarge “get the job done” even if the API is horribly designed. For example, some might argue if the API does not follow CRUD, HATEOAS, or etc they are bad designs, but I digress.

![DLL Hell][9]

### A Meh Solution

So the current solution to support multiple versions of the Mesos Isolator interface from a single code base has been to use ifdefs. Yea, unfortunately the solution isn’t as elegant as it could be, but with the break in backwards compatibility in the Isolator interface, it leaves us with very little choice. This is also compounded by the fact that this isn’t the only interface that is changing between versions. There are other Mesos support libraries in which the interface is in flux and adding an abstraction layer among all these functions, classes, and structures might take you down a rabbit hole that will leave you rocking yourself in the fetal position for months crying “so many functions”.

Since we are using ifdefs, that unfortunately means our solution is a compile time solution and we therefore need to build every version that we plan on supporting. We do that in our Makefile as see below.

In the [Makefile][10] on lines 8 and 818 respectively:  
`ISO_VERSIONS := 0.23.1 0.24.1 0.25.0 0.26.0`

`$(foreach V,$(ISO_VERSIONS),$(eval $(call ISOLATOR_BUILD_RULES,$(V))))`

Then we take that version and create an integer representation of the version by stripping out the periods from the version we are compiling. Represented by the `MESOS_VERSION_INT` preprocessor directive below. An example of that would be version 0.24.1 becomes 0241.

In the [Makefile][10] lines 790-795:  
`$$(ISO_MAKEFILE_$1): CXXFLAGS=-I$$(GLOG_OPT_DIR)/include
-I$$(PICOJSON_OPT_DIR)/include
-I$$(BOOST_OPT_DIR)
-I$$(PBUF_OPT_DIR)/include
-DMESOS_VERSION_INT=$$(subst .,,$1)
$$(ISO_MAKEFILE_$1): $$(ISO_CONFIGURE_$1) $$(ISO_DEPS_$1)`

Then we compile the project using the `MESOS_VERSION_INT` and make alterations to the code to handle the ifdefs.

In the [docker_volume_driver_isolator.hpp][11]:  
`if MESOS_VERSION_INT != 0 && MESOS_VERSION_INT < 0240<br />
class DockerVolumeDriverIsolator: public mesos::slave::IsolatorProcess<br />
else<br />
class DockerVolumeDriverIsolator: public mesos::slave::Isolator<br />
endif`

`if MESOS_VERSION_INT != 0 && MESOS_VERSION_INT < 0240<br />
process::Future<Nothing> recover(
const std::list<ExecutorRunState>& states,
const hashset<ContainerID>& orphans);<br />
else<br />
virtual process::Future<Nothing> recover(
const std::list<ContainerState>& states,
const hashset<ContainerID>& orphans);<br />
endif`

`if MESOS_VERSION_INT != 0 && MESOS_VERSION_INT < 0240<br />
process::Future<Option<CommandInfo>> prepare(
const ContainerID& containerId,
const ExecutorInfo& executorInfo,
const std::string& directory,
const Option<std::string>& rootfs,
const Option<std::string>& user);<br />
else<br />
process::Future<Option<ContainerPrepareInfo>> prepare(
const ContainerID& containerId,
const ExecutorInfo& executorInfo,
const std::string& directory,
const Option<std::string>& user);<br />
endif`

So that is it. Again, not the most elegant solution but unfortunately addressing the backwards compatibility is going to be very difficult to resolve going forward as it would require retrofitting older versions of Mesos. That could get pretty ugly to change architecture in the form of patches or even a minor release. Odds are this is not going to go away any time soon. I hope this helps other developers out there looking to create Mesos Isolators (and Mesos Frameworks because it looks like the Framework interfaces may have the same problem).

 [1]: http://mesos.apache.org/
 [2]: https://mesosphere.com/
 [3]: http://www.emc.com/
 [4]: https://raw.githubusercontent.com/dvonthenen/blog/master/images/windows10install.png
 [5]: https://github.com/emccode/mesos-module-dvdi/releases/tag/v0.4.0
 [6]: https://github.com/emccode/mesos-module-dvdi
 [7]: https://github.com/apache/mesos/blob/0.23.1/include/mesos/slave/isolator.hpp
 [8]: https://github.com/apache/mesos/blob/0.24.1/include/mesos/slave/isolator.hpp
 [9]: https://raw.githubusercontent.com/dvonthenen/blog/master/images/dllhell.jpg
 [10]: https://github.com/emccode/mesos-module-dvdi/blob/v0.4.0/Makefile
 [11]: https://github.com/emccode/mesos-module-dvdi/blob/v0.4.0/isolator/isolator/docker_volume_driver_isolator.hpp