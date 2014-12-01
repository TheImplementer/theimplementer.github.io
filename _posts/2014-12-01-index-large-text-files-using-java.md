---
layout: post
title: Index large text files using Java
---

A couple of weeks ago I found myself in a situation where I needed to index the content of a text file whose size was quite big, more than the available RAM in the PC.
Basically, the idea was to replicate the behaviour of a database index by going through the file and looking for specific lines that we wanted to index and than saving the line along with the position in the file.
We can later use the position to move quickly inside the file to retrieve the desired information.

In a first attempt we tried using the **RandomAccessFile** provided by Java. The method **getFilePointer** seemed to be exactly what we wanted, as the documentation says:

{% highlight %}
Returns the current offset in this file.
{% endhighlight %}

The class was also really convenient because it provides the method **readLine** to iterate over the lines in the file.
However, we soon realised that, even if the solution was working correctly, it was taking a huge amount of time to complete the indexing process. (in the order of hours)
This wasn't really a problem, but it would have been nice to improve the performance. 

Therefore, after investigating a little bit, we found out that the problem lies in the way the **readLine** method is implemented. In particular, the method reads lines' character one at a time, without any kind of buffering strategy. This, of course, has the effect of making the reading of a huge file a very slow process.

At this point we thought we could use something smarter, like Java's **BufferedReader**. Since the class doesn't provide a way to retrieve the position of the reading pointer inside the file, we had to count the characters retrieved using the **readLine** manually.
In this case the problem was another one. Reading the documentation of the **readLine** method we can see the following:

{% highlight %}
Reads a line of text. A line is considered to be terminated by any one of a line feed ('\n'), a carriage return ('\r'), or a carriage return followed immediately by a linefeed.

Returns:
A String containing the contents of the line, not including any line-termination characters, or null if the end of the stream has been reached`
{% endhighlight %}

Can you see it?
The problem is that the line-termination character are not included in the read line, and we couldn't tell if the line was terminated by a single character (\n or \r) or two characters (\n\r).

At this point we decided to implement a custom solution. This had to apply a buffering strategy while reading the file, and at the same time it should provide us the real length of the line in the file.

You can see the result at the following link:

[https://github.com/theimplementer/file-lines-iterator]()
	
The resulting class is an iterator that generates the specified file's lines along with their length by returning objects of type **Line**:

{% highlight java %}
public class Line {

    private final String content;
    private final int length;

    ...
}
{% endhighlight %}

Using this solution we could count the characters of the lines correctly, and we brought the indexing time down from ~4hrs to ~5min.
