<properties
   pageTitle="Learn about Apache Storm on HDInsight | Azure"
   description="Learn how you can use Apache Storm on HDInsight to build real-time data analytics in the Azure cloud."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#Storm on HDInsight overview

Apache Storm on HDInsight allows you to create distributed, real-time data processing solutions in the Azure environment by using <a href="http://hadoop.apache.org/" target="_blank">Apache Hadoop</a>.

##What is Apache Storm?

Apache Storm is a distributed, fault-tolerant, open-source computation system that allows you to process data in real-time with Hadoop. Storm solutions can also provide guaranteed processing of data, with the ability to replay data that was not successfully processed the first time.

##Why use Storm on HDInsight?

Apache Storm on HDInsight is a managed cluster integrated into the Azure environment. It provides the following key benefits:

* Performs as a managed service with an SLA of 99.9% up time

* Use the language of your choice: Provides support for Storm components written in **Java**, **C#**, and **Python**

	* Supports a mix of programming languages: Read data using Java, then process it using C#

	* Use the **Trident** Java interface to create Storm topologies that support "exactly once" processing of messages, "transactional" datastore persistence, and a set of common stream analytics operations

* Includes built-in scale-up and scale-down features: Scale an HDInsight cluster with no impact to running Storm topologies

* Integrate with other Azure services, including Event Hub, Azure Virtual Network, SQL Database, Blob storage, and DocumentDB

	* Combine the capabilities of multiple HDInsight clusters by using Azure Virtual Network: Create analytic pipelines that use HDInsight, HBase, or Hadoop clusters

For a list of companies that are using Apache Storm, see <a href="https://storm.apache.org/documentation/Powered-By.html" target="_blank">Companies Using Apache Storm</a>.

To get started using Storm, see [Get started with Storm on HDInsight][gettingstarted].

###Ease of provisioning

You can provision a new Storm on HDInsight cluster in minutes. Specify the cluster name, size, administrator account, and the storage account. Azure will create the cluster, including sample topologies and a web-management dashboard.

The following is an example of provisioning a Storm on HDInsight cluster by using the Azure portal. You can also provision Storm clusters by using <a href="../install-configure-powershell/" target="_blank">Azure PowerShell</a>.

![An example of the quick-create cluster form in the portal](./media/hdinsight-storm-overview/quick-create.png)

Within 15 minutes of submitting the request, you will have a new Storm cluster running and ready for your first real-time analytics pipeline.

###Ease of use

If you use Visual Studio, the HDInsight Tools for Visual Studio allow you to create C# and hybrid C#/Java topologies, and then submit them to your Storm on HDInsight cluster.  

![Storm Project creation](./media/hdinsight-storm-overview/createproject.png)

HDInsight Tools for Visual Studio also provides an interface that allows you to monitor and manage Storm topologies on a cluster.

![Storm management](./media/hdinsight-storm-overview/stormview.png)

For an example of using the HDInsight Tools to create a Storm application, see <a href="../hdinsight-storm-develop-csharp-visual-studio-topology/" target="_blank">Develop C# Storm topologies with the HDInsight Tools for Visual Studio</a>

For more information about the HDInsight Tools for Visual Studio, see <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Get started using the HDInsight Tools for Visual Studio</a>.

Each Storm on HDInsight cluster also provides a web-based Storm Dashboard that allows you to submit, monitor, and manage Storm topologies running on the cluster. 

![Storm dashboard](./media/hdinsight-storm-overview/dashboard.png)

For more information about using the Storm Dashboard, see <a href="../hdinsight-storm-deploy-monitor-manage-topology/" target="_blank">Deploy and manage Apache Storm topologies on HDInsight</a>.

Storm on HDInsight also provides easy integration with Azure Event Hubs through the **Event Hub Spout**. This is available on each storm cluster at **%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar**. For examples of using this spout in a Storm topology, see <a href="../service-bus-event-hubs-c-storm-getstarted/" target="_blank">Getting started with Event Hubs</a> and <a href="../hdinsight-storm-sensor-data-analysis/" target="_blank">Analyzing sensor data with Storm and HBase</a>.

###Reliability

Apache Storm always guarantees that each incoming message will be fully processed, even when the data analysis is spread over hundreds of nodes. 

The **Nimbus node** provides similar functionality to the Hadoop JobTracker, and it assigns tasks to other nodes in the cluster through **Zookeeper**. Zookeeper nodes provide coordination for the cluster and facilitate communication between Nimbus and the **Supervisor** process on the worker nodes. If one processing node goes down, the Nimbus node is informed, and it assigns the task and associated data to another node.

The default configuration for Apache Storm is to have only one Nimbus node. Storm on HDInsight runs two Nimbus nodes. If the primary node fails, the HDInsight cluster will switch to the secondary node while the primary node is recovered.

![Diagram of nimbus, zookeeper, and supervisor](./media/hdinsight-storm-overview/nimbus.png)

###Scale

Although you can specify the number of nodes in your cluster during creation, you may want to grow or shrink the cluster to match workload. All HDInsight clusters allow you to change the number of nodes in the cluster, even while processing data.

![data nodes instance count in the portal](./media/hdinsight-storm-overview/scale.png)

###Support

Storm on HDInsight comes with full enterprise-level 24/7 support. Storm on HDInsight also has an SLA of 99.9%. That means we guarantee that the cluster will have external connectivity at least 99.9% of the time.

##Common scenarios for real-time analytics

The following are some common scenarios for which you might use Apache storm on HDInsight. For information about real-world scenarios, read <a href="https://storm.incubator.apache.org/documentation/Powered-By.html" target="_blank">How companies are using Storm</a>.

* Internet of Things (IoT)
* Fraud detection
* Social analytics
* Extract, Transform, Load (ETL)
* Network monitoring
* Search
* Mobile engagement

##How is data in HDInsight Storm processed?

Apache Storm runs **topologies** instead of the MapReduce jobs that you may be familiar with in HDInsight or Hadoop. A Storm on HDInsight cluster contains two types of nodes: head nodes that run **Nimbus** and worker nodes that run **Supervisor**.

* **Nimbus**: Similar to the JobTracker in Hadoop, it is responsible for distributing code throughout the cluster, assigning tasks to virtual machines, and monitoring for failure. HDInsight provides two Nimbus nodes, so there is no single point of failure for Storm on HDInsight

* **Supervisor**: The supervisor for each worker node is responsible for starting and stopping **worker processes** on the node.

* **Worker process**: Runs a subset of a **topology**. A running topology is distributed across many worker processes throughout the cluster.

* **Topology**: Defines a graph of computation that processes **streams** of data. Unlike MapReduce jobs, topologies run until you stop them.

* **Stream**: An unbound collection of **tuples**. Streams are produced by **spouts** and **bolts**, and they are consumed by **bolts**.

* **Tuple**: A named list of dynamically typed values.

* **Spout**: Consumes data from a data source and emits one or more **streams**.

	> [AZURE.NOTE] In many cases, data is read from a queue, such as Kafka, Azure Service Bus queues, or Event hubs. The queue ensures that data is persisted if there is an outage.

* **Bolt**: Consumes **streams**, performs processing on **tuples**, and may emit **streams**. Bolts are also responsible for writing data to external storage, such as a queue, HDInsight, HBase, a blob, or other data store.

* **Apache Thrift**: A software framework for scalable cross-language service development. It allows you to build services that work between C++, Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, JavaScript, Node.js, Smalltalk, and other languages.

	* **Nimbus** is a Thrift service, and a **topology** is a Thrift definition, so it is possible to develop topologies using a variety of programming languages. 

For more information about Storm components, see the [Storm tutorial][apachetutorial] at apache.org.


##What programming languages can I use?

The Storm on HDInsight cluster provides support for C#, Java, and Python.

<h3>C#</h3>

The HDInsight Tools for Visual Studio allow .NET developers to design and implement a topology in C#. You can also create hybrid topologies that use Java and C# components.

For more information, see [Develop C# topologies for Apache Storm on HDInsight using Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology).

###Java

Most Java examples you encounter will be plain Java or Trident. Trident is a high-level abstraction that makes it easier to do things such as joins, aggregations, grouping, and filtering. However, Trident acts on batches of tuples, whereas a raw Java solution processes a stream one tuple at a time.

For more information about Trident, see the [Trident tutorial](https://storm.incubator.apache.org/documentation/Trident-tutorial.html) at apache.org.

For examples of raw Java and Trident topologies, see the **%storm_home%\contrib\storm-starter** directory on your HDInsight Storm cluster.

##What are some common development patterns?

###Guaranteed message processing

Storm can provide different levels of guaranteed message processing. For example, a basic Storm application can guarantee at-least-once processing, and Trident can guarantee exactly-once processing.

For more information, see [Guarantees on data processing](https://storm.apache.org/about/guarantees-data-processing.html) at apache.org.

###IBasicBolt

The pattern of reading an input tuple, emitting zero or more tuples, and then acking the input tuple immediately at the end of the execute method is very common, and Storm provides the [IBasicBolt](https://storm.apache.org/apidocs/backtype/storm/topology/IBasicBolt.html) interface to automate this pattern.

###Joins

Joining two streams of data will vary between applications. For example, you could join each tuple from multiple streams into one new stream, or you could join only batches of tuples for a specific window. Either way, joining can be accomplished by using [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29), which is a way of defining how tuples are routed to bolts.

In the following Java example, fieldsGrouping is used to route tuples that originate from components "1", "2", and "3" to the **MyJoiner** bolt.

	builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2")); 

###Batching

Batching can be accomplished several ways. With a basic Storm Java topology, you might use simple counter to batch X number of tuples before emitting them, or use an internal timing mechanism known as a "tick tuple" to emit a batch every X seconds.

For an example of using tick tuples, see [Analyzing sensor data with Storm and HBase on HDInsight](/documentation/articles/hdinsight-storm-sensor-data-analysis.md).

If you are using Trident, it is based on processing batches of tuples.

###Caching

In-memory caching is often used as a mechanism for speeding up processing because it keeps frequently used assets in memory. Because a topology is distributed across multiple nodes, and multiple processes within each node, you should consider using [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) to ensure that tuples containing the fields that are used for cache lookup are always routed to the same process. This avoids duplication of cache entries across processes.

###Streaming top N

When your topology depends on calculating a "top N" value, such as the top 5 trends on Twitter, you should calculate the top N value in parallel and then merge the output from those calculations into a global value. This can be done by using [fieldsGrouping](http://javadox.com/org.apache.storm/storm-core/0.9.1-incubating/backtype/storm/topology/InputDeclarer.html#fieldsGrouping%28java.lang.String,%20backtype.storm.tuple.Fields%29) to route by field to the parallel bolts (which partitions the data by field value), and then route to a bolt that globally determines the top N value.

For an example of this, see the [RollingTopWords](https://github.com/nathanmarz/storm-starter/blob/master/src/jvm/storm/starter/RollingTopWords.java) example.

##Next steps

* [Getting Started with Storm on HDInsight][gettingstarted]

* [Develop C# topologies for Apache Storm on HDInsight using Visual Studio](../hdinsight-storm-develop-csharp-visual-studio-topology)

* [Develop Java-based topologies for Apache Storm on HDInsight](../hdinsight-storm-develop-java-topology)

* [Analyzing sensor data with Storm and HBase on HDInsight](/documentation/articles/hdinsight-storm-sensor-data-analysis)

* [Analyzing Twitter trending topics with Apache Storm on HDInsight](../hdinsight-storm-twitter-trending)

[stormtrident]: https://storm.incubator.apache.org/documentation/Trident-API-Overview.html
[samoa]: http://yahooeng.tumblr.com/post/65453012905/introducing-samoa-an-open-source-platform-for-mining
[apachetutorial]: https://storm.incubator.apache.org/documentation/Tutorial.html
[gettingstarted]: /documentation/articles/hdinsight-storm-getting-started
