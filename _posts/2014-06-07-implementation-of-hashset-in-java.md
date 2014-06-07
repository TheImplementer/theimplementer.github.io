---
layout: post
title: Implementation of HashSet in Java
---

In this post I'll be talking about how the class HashSet is implemented in Java. We'll be having a look at the source code included with the OpenJDK project.

First of all, let's define what a set is. A set is an **unordered** collection of elements in which each of the elements is guaranteed to be **unique**. To make a comparison, in a list the elements have some order and it is perfectly fine to have duplicates.

Let's now move to some code. You can find the source code for the OpenJDK's HashSet source code at the following link: [http://hg.openjdk.java.net/jdk8/tl/jdk/file/183a8c520b4a/src/share/classes/java/util/HashSet.java](http://hg.openjdk.java.net/jdk8/tl/jdk/file/183a8c520b4a/src/share/classes/java/util/HashSet.java)

The first thing to notice from the code is that the class is actually backed up by an HashMap, whose keys are of the same class as the elements contained in the set, and the values are of type Object:

{% highlight java %}
public class HashSet<E>
    extends AbstractSet<E>
    implements Set<E>, Cloneable, java.io.Serializable
{
    ...
    private transient HashMap<E,Object> map;
    ...
{% endhighlight %}

Later, the code defines a dummy constant value to be used inside the map:

{% highlight java %}
private static final Object PRESENT = new Object();
{% endhighlight %}

All the methods of the HashSet can now use the enclosed map to implement the expected behaviour for them. Let's see a couple of examples:

{% highlight java %}
    public Iterator<E> iterator() {
        return map.keySet().iterator();
    }
{% endhighlight %}

what we get when we ask the set for an iterator is actually an iterator over the keys of the map.

{% highlight java %}
    public boolean contains(Object o) {
        return map.containsKey(o);
    }
{% endhighlight %}

the check of the presence of an item inside a set is implemented by checking if the map contains the specified item as a key.


{% highlight java %}
    public boolean add(E e) {
        return map.put(e, PRESENT)==null;
    }
{% endhighlight %}

adding an item into a set becomes just a matter of inserting the dummy object into the map using the specified element as key. The fact that the put method of the map returns the previous item associated with the key allows returning if the set contained the item by means of a simple equality check.

In this article I presented some implementation details about the HashSet class that show a smart reusage of an existing class to implement the functionalities of another one. Hope you enjoyed it!

See you soon!
