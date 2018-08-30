---
layout: post
title: "Akka Cluster With Router Pools"
date: 2016-03-12T00:00:00+08:00
tags: akka scala
---

With Akka cluster we have two options while creating routers. Group and Pool. The pool option is better suited when we are trying to scale a computing operation across nodes. 

The activator template has example of this kind of router

The conf file for a Pool router:

{% highlight bash %}
akka.actor.deployment {
  /statsService/singleton/workerRouter {
    router = consistent-hashing-pool
    cluster {
      enabled = on
      max-nr-of-instances-per-node = 16
      allow-local-routees = on
      use-role = compute
    }
  }
}
{% endhighlight %}

Assuming that each machine has two CPU with four cores, if we have 16 instances then there are two actors per core.
The router here is defined with consistent-hashing-pool and allow-local-routees is set as off so that processing should be done on remote cores.

If we want to avoid the hassle of using a ConsistentHashableEnvelope when sending messages to the router, we can tell the StatsJob that it can use a unique ID as a consistent hash key.

{% highlight bash %}
final case class StatsJob(text: String) extends ConsistentHashable {
  override def consistentHashKey = id.toString
}
{% endhighlight %}

We define a StatsService class which functions as an intermediary between the workers and the router and ensures that duplicate jobs are not dispatched and that failed jobs are logged and restarted. The StatsService defines the router internally.


{% highlight bash %}
class StatsService extends Actor {
  val workerRouter = context.actorOf(FromConfig.props(Props[StatsWorker]),
    name = "workerRouter")

  def receive = {
    case StatsJob(text) if text != "" =>
      val words = text.split(" ")
      val replyTo = sender() 

      val aggregator = context.actorOf(Props(
        classOf[StatsAggregator], words.size, replyTo))

      words foreach { word =>
        workerRouter.tell(word, aggregator)
      }
  }
}
{% endhighlight %}

Then we create the cluster singleton manager on each node by using the akka.cluster.singleton.ClusterSingletonManager
It manages one singleton actor instance among all cluster nodes or a group of nodes tagged with a specific role.

example: ClusterSingletonManagerSettings(system).withRole("compute"))

Here the singleton is created only on the nodes tagged as role "compute"

The actual singleton actor is started by the ClusterSingletonManager on the oldest node by creating a child actor from supplied Props. ClusterSingletonManager makes sure that at most one singleton instance is running at any point in time.The cluster failure detector will notice when oldest node becomes unreachable due to things like JVM crash, hard shut down, or network failure. Then a new oldest node will take over and a new singleton actor is created. 

{% highlight bash %}
 system.actorOf(ClusterSingletonManager.props(
        singletonProps = Props[StatsService],
        terminationMessage = PoisonPill,
        settings = ClusterSingletonManagerSettings(system).withRole("compute")),
        name = "statsService")
{% endhighlight %}


We can send the message to the singleton StatsService by using the singletonProxy. This proxy is provided by akka.cluster.singleton.ClusterSingletonProxy and keeps track of current singleton manager in cluster. It will route all messages to the current instance of the singleton. 


{% highlight bash %}
system.actorOf(ClusterSingletonProxy.props(singletonManagerPath = "/user/statsService",
        settings = ClusterSingletonProxySettings(system).withRole("compute")),
        name = "statsServiceProxy")
{% endhighlight %}

All the jobs to the cluster would be send to the path "/user/statsServiceProxy" which is the path of Singleton Proxy which will route to the current cluster manager. The cluster manager will in turn evenly distribute work to the available routees in the pool. If a new node is added, the router will automatically start new routees in the pool and start sending work to them.

The overall workflow for the average word length calculator
startup 

for each node {

- get the config
- get the actor system based on config
- create-singleton-manager (statsService)
- create singleton-proxy (statsServiceProxy)

}


statsService{

 - creates worker Router (workerRouter [statsWorker])
 - creates the aggregator 
 - on message receive send message to worker through workerRouter. 
 -- Set the sender as aggregator so that once work is done, worker can send result to aggregator
 -- Can select the hashing function for the router while sending the work 
}

Reference:

- <http://doc.akka.io/docs/akka/2.4.1/scala/cluster-singleton.html>
- <http://www.lightbend.com/activator/template/akka-sample-cluster-scala>



