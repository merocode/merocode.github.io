---
layout: post
title:  "Visualizing Graphs"
date:   2014-06-23 07:52:00
tags: 
- groovy
- java
- graphs
- visualize
- ubigraph
---

When I first came across complex data structures like graphs, trees or even linked lists, I always wondered how easy it would be if I could just visuallize these data structures. I often resort to using text output or even drawing on a paper as a means of visualizing them. However, fairly recently I stumbled upon a tool that lets be visualize these structures in real time, and in a very easy way. [Ubigraph](http://ubietylab.net/ubigraph/index.html) has api for several languages and it is pretty easy to get started with.

## Installation

Simply [download](http://ubietylab.net/ubigraph/content/Downloads/index.php) the proper files for your system and run
<pre>
  bin/ubigraph_server
</pre>

This will start an XML-RPC server that you can talk to via api calls using any language of your choice.

If you want to use a Java client, there there is a few additional installation process. The UbiGraph site has the necessary instructions, however the Apache site it links to for downloading the necessary jar files returned 404 for me. So I downloaded the required dependencies from [http://www.java2s.com/Code/Jar/CatalogJar.htm](http://www.java2s.com/Code/Jar/CatalogJar.htm) site. These files need to be in your Java Classpath.
<pre>
  exist-dependency-ws-commons-util-1.0.2.jar
  xmlrpc-3.0-common.jar
  xmlrpc-3.0.jar
</pre>
Similarly, I added <code>ubigraph.jar</code> in my Java Classpath as well which can be found inside `examples/Java` folder of the downloaded UbiGraph folder.

## Quick Test

Ubigraph can be tested with any java client. Here, I use a [groovy](http://groovy.codehaus.org/) script `test.groovy` to see if I can draw some nodes and vertices.

{% highlight groovy %}
import org.ubiety.ubigraph.UbigraphClient;

def graph = new UbigraphClient()
graph.clear()
def center = graph.newVertex()
(1..100).each {
  def v = graph.newVertex()
  graph.newEdge(v, center)
}
{% endhighlight %}

![Screen Shot](/assets/ubigraph1.png)

Here is another Simple Stack implementation in Java. The code pushes the numbers 1 to 10 onto a Stack and pops 9 of them.

{% highlight java %}
import org.ubiety.ubigraph.UbigraphClient;

public class StackGraph<Item> {

  Node head;
  UbigraphClient graph;
  int count = 0;

  private class Node<Item> {
    Item item;
    Node next;
    int vertex;
  }

  public StackGraph() {
    graph = new UbigraphClient();
    graph.clear();
  }

  public void push(Item item) {
    Node oldHead = head;
    head = new Node();
    head.item = item;
    head.vertex = graph.newVertex();
    graph.setVertexAttribute(head.vertex, "label", item.toString());
    graph.setVertexAttribute(head.vertex, "shape", "sphere");
    head.next = oldHead;
    if(oldHead == null) {
      graph.setVertexAttribute(head.vertex, "color", "#FF0000");
    }
    else {
      int edge = graph.newEdge(oldHead.vertex, head.vertex);
      graph.setEdgeStyleAttribute(count, "arrow", "true");
    }
    count++;
  }

  public Item pop() {
    Item item = (Item)head.item;
    graph.removeVertex(head.vertex);
    head = head.next;
    count--;
    return item;
  }

  public static void main(String[] args) {
    StackGraph<Integer> stack = new StackGraph();
    for(int i = 0; i < 10; i++) {
      stack.push(i);
      try {
        Thread.sleep(500);
      }
      catch(Exception e) {
      }
    }

    for(int i = 0; i < 9; i++) {
      stack.pop();
      try {
        Thread.sleep(500);
      }
      catch(Exception e) {
      }
    }
  }
}
{% endhighlight %}
![image](/assets/stack.gif)

Finally, here is a simple Undirected Graph with 20 vertices. The code randomly generates edges between the vertices which are then drawn to the screen.

{% highlight java %}
import java.util.Random;
import java.util.ArrayList;
import org.ubiety.ubigraph.UbigraphClient;

public class AdjGraph {

  UbigraphClient ubigraph;
  private int V;
  private int E;
  private ArrayList<Integer>[] adj;

  public AdjGraph(int vertices) {
    ubigraph = new UbigraphClient();
    ubigraph.clear();
    V = vertices;
    E = 0;
    adj = (ArrayList<Integer>[]) new ArrayList[V];
    for(int i = 0; i < V; i++) {
      adj[i] = new ArrayList<Integer>();
      ubigraph.newVertex(i);
      ubigraph.setVertexAttribute(i, "label", i+"");
      ubigraph.setVertexAttribute(i, "color", "#FF8000");
      ubigraph.setVertexAttribute(i, "shape", "torus");
      try {
        Thread.sleep(500);
      }
      catch(Exception e) {
      }
    }
  }

  public void addEdge(int from, int to) {
    ubigraph.newEdge(from, to);
    ubigraph.setEdgeStyleAttribute(E, "size", "2.5");
    ubigraph.setEdgeStyleAttribute(E, "spline", "true");
    ubigraph.setEdgeStyleAttribute(E, "color", "#FF0000");
    adj[from].add(to);
    adj[to].add(from);
    E++;
  }

  public static void main(String[] args) {
    AdjGraph graph = new AdjGraph(20);
    Random random = new Random();
    for(int i = 0; i < 20; i++) {
      graph.addEdge(random.nextInt(20), random.nextInt(20));
      try {
        Thread.sleep(500);
      }
      catch(Exception e) {
      }
    }
  }
}
{% endhighlight %}
![image](/assets/graph.gif)

It is now very easy to see which vertices are connected, how many connected components exist as well as various other information about the graph simply by looking at the visualization.


