---
post_title: 'Enabling External Storage on Mesos Frameworks'
layout: post
published: true
---
There has been a huge push to take containers to the next level by twisting them to do much more. So much more in fact that many are starting to use them in ways that were never originally intended and even going against the founding principles of containers. The most noteworthy principle of containers being left on the designing room floor is without a doubt is being "stateless". It is pretty evident that this trend is only accelerating... just doing a simple search of popular traditional databases in [Docker Hub](https://hub.docker.com/) yields results like [MySQL](https://hub.docker.com/_/mysql/), [MariaDB](https://hub.docker.com/_/mariadb/), [Postgres](https://hub.docker.com/_/postgres/), and yes by even having [OracleLinux](https://hub.docker.com/_/oraclelinux/) in Docker Hub, Oracle is suggesting you might try running Oracle in a Docker container. Then there is all the NoSQL implementations like [Elastic Search](https://hub.docker.com/_/elasticsearch/), [Cassandra](https://hub.docker.com/_/cassandra/), [MongoDB](https://hub.docker.com/_/mongo/), and [CouchBase](https://hub.docker.com/_/couchbase/) just to name a few. We are going to take a look to see how we can bring these workloads into the next evolution of stateful containers using Elastic Search as a proposed model.

![After Though](https://raw.githubusercontent.com/dvonthenen/blog/master/images/afterthought.jpg)

 The problem with stateful containers today is that pretty much every implementation of a container whether its [Docker](http://www.docker.com/), [Apache Mesos](http://mesos.apache.org/), or etc has been architected with those original principles, such as being stateless, in mind. When the container goes away, anything associated with the container is gone. This obviously makes it difficult to maintain configuration or keep long term data around. Why make this tradeoff to begin with? It keeps the design simple. The problem is useful applications all have state somewhere. As a response, container implementations enabled the ability to store data on the local disks of compute nodes; thus tying workloads to a single node and on the failure of a particular node, you would lose all your local data. Not good for high availability huh?

### Enter the Elastic Search Mesos Framework

 This brings me to a recently submitted a [Pull Request](https://github.com/mesos/elasticsearch/pull/489) to the [Elastic Search (ES) Mesos Framework](https://github.com/mesos/elasticsearch) project on GitHub to add support for External Storage orchestration, but more importantly to enable management of those external storage resources among Mesos slave/agent nodes. Before I jump into talking about the ES Framework, I probably should quickly talk about Mesos Frameworks in general. A Mesos Framework is a way to specialize a particular workload or application. This specialization can come in the form of tuning the application to best utilize hardware, like a GPU for heavy graphics processing, on a given slave node or even distributing tasks that are scaled out to place them in different racks within a datacenter for high availability. When an resource offer is passed along to a Framework by Mesos, the Framework can evaluate the offers and deploy specialized tasks or applications in the form of Executors to slave/agent nodes (seen below).

![Mesos Architecture - Picture Thanks to DigitalOcean](https://raw.githubusercontent.com/dvonthenen/blog/master/images/mesos_architecture.png)

The ES Framework behaves in the same way described above. The design of the ES Framework and Executor have been done in such a way that both components have been implemented in Docker containers. The ES Framework is deployed to Marathon via Docker and by default the Framework will create 3 Elastic Search nodes based on a special list of criteria. If the offer meets that criteria, an Elastic Search Executor in the form of a Docker container will be created to the Mesos slave/agent node representing that offer. Within that Executor image holds the task which in this case is an Elastic Search node.

### Deep Dive: How it Works

Let's do a deep dive on the Pull Request and discuss why I made some of the decisions that I did. I first broke apart the [OfferStrategy.java](https://github.com/dvonthenen/elasticsearch/blob/feature/externalvolumesupport/scheduler/src/main/java/org/apache/mesos/elasticsearch/scheduler/OfferStrategy.java) into a base class containing everything common to a "normal" Elastic Search strategy versus one that will make use of external storage. Then the [OfferStrategyNormal.java](https://github.com/dvonthenen/elasticsearch/blob/feature/externalvolumesupport/scheduler/src/main/java/org/apache/mesos/elasticsearch/scheduler/OfferStrategyNormal.java) retains the original functionality and behavior of this ES Framework which is on by default. Then I created the [OfferStrategyExternalStorage.java](https://github.com/dvonthenen/elasticsearch/blob/feature/externalvolumesupport/scheduler/src/main/java/org/apache/mesos/elasticsearch/scheduler/OfferStrategyExternalStorage.java) which removes all checks for storage requirements. Since the storage used in this mode is all managed externally, the framework does not need to take storage requirements into account when it looks at the criteria for deployment.

The critical piece in assigning External Volumes to Elastic Search nodes is to be able to unique associate a set of Volumes containing configuration and data of each elastic search node represented by /tmp/config and /data. That means we need to create at minimum runtime unique IDs. What do I mean runtime unique? It means that if there exists a ES node with an identifier of ID2, there exists no other node with an ID2. If an ID is freed lets say ID2 from a Mesos slave/agent node failure, we make every attempt to reuse that ID2. This identifier is defined as a task environment variable as seen in [ExecutorEnvironmentalVariables.java](https://github.com/dvonthenen/elasticsearch/blob/feature/externalvolumesupport/scheduler/src/main/java/org/apache/mesos/elasticsearch/scheduler/configuration/ExecutorEnvironmentalVariables.java).

<pre>
addToList(ELASTICSEARCH_NODE_ID, Long.toString(lNodeId));
LOGGER.debug("Elastic Node ID: " + lNodeId);

private void addToList(String key, String value) {
  envList.add(getEnvProto(key, value));
}
</pre>

Why an environment variable? Because when the task and therefore the Executor is lost, the reference to the ES Node ID is freed so that when a new ES Node is created to replace the failed node, the ES Node ID is recycled. This is a time where we don't want presistence! How do we determine what Node ID we should be using when selecting a new or recycling a Node ID? We do this using the following function in [ClusterState.java](https://github.com/dvonthenen/elasticsearch/blob/feature/externalvolumesupport/scheduler/src/main/java/org/apache/mesos/elasticsearch/scheduler/state/ClusterState.java):

<pre>
public long getElasticNodeId() {
    List<TaskInfo> taskList = getTaskList();

    //create a bitmask of all the node ids currently being used
    long bitmask = 0;
    for (TaskInfo info : taskList) {
        LOGGER.debug("getElasticNodeId - Task:");
        LOGGER.debug(info.toString());
        for (Variable var : info.getExecutor().getCommand().getEnvironment().getVariablesList()) {
            if (var.getName().equalsIgnoreCase(ExecutorEnvironmentalVariables.ELASTICSEARCH_NODE_ID)) {
                bitmask |= 1 << Integer.parseInt(var.getValue()) - 1;
                break;
            }
        }
    }
    LOGGER.debug("Bitmask: " + bitmask);

    //the find out which node ids are not being used
    long lNodeId = 0;
    for (int i = 0; i < 31; i++) {
        if ((bitmask & (1 << i)) == 0) {
            lNodeId = i + 1;
            LOGGER.debug("Found Free: " + lNodeId);
            break;
        }
    }

    return lNodeId;
}
</pre>

We get the current running task list, find out which ones have the environment variable set, build a bit mask, then walk the bitmask starting from the least significant bit until we have a free ID. Fairly simple. As someone who doesn't run Elastic Search in production, it was pointed out to me this would only support 32 nodes so there is a future commit that will be done to make this generic for an unlimited number of nodes.

### Let's Do This Thing

The enable this, all you need to do is have your Docker Volume Driver installed, like [REX-Ray](https://github.com/emccode/rexray) for example, pre-create the volumes you plan on using based on the parameter `--frameworkName` (default: elasticsearch) appended with the node id and config/data (example: elasticsearch1config and elasticsearch1data), and then start the ES Framework with the command line parameter `--externalVolumeDriver=rexray` or what ever volume driver you happen to be using. You are all set! Pretty easy huh? Interested in seeing more? You can find a demo on YouTube located below.

 <iframe width="420" height="315" src="https://www.youtube.com/embed/0dhlcft9aWc" frameborder="0" allowfullscreen></iframe>

BONUS! The Elastic Search Framework has a facility (although only recommended for very advanced users) for using the Elastic Search JAR directly on the Mesos slave/agent node and in that case, code was also added in this Pull Request to use the [mesos-module-dvdi](https://github.com/emccode/mesos-module-dvdi), which is a Mesos Isolator, to create and mount your external volumes. You just need to install [mesos-module-dvdi](https://github.com/emccode/mesos-module-dvdi) and [DVDCLI](https://github.com/emccode/dvdcli).

The good news is that the [Pull Request](https://github.com/mesos/elasticsearch/issues/490) has been accepted and it is currently slated for the 8.0 release of Elastic Search Framework. The bad news is the next release looks like to be version 7.2. So you are going to have to wait a little longer before you get an official release with this External Volume support.

### Frameworks.NEXT

What's up next? This was a good exercise in adding on to an existing Mesos Framework and Executor and the [{code} team](http://emccode.github.io/) may potentially have a Framework of our own on the way. Stay tuned!

![Drops the Mic](https://raw.githubusercontent.com/dvonthenen/blog/master/images/micdrop.jpg)
