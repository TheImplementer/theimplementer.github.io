---
layout: post
title: Displaying items details when clicking on an item in a ListView
---

For the first post of this blog I want to talk about something related to Android.
In particular, the other day I was working on an Android application, and I wanted it to behave like this:

![Application Flow]({{ site.url }}/images/post_image.png)

My application has an activity that contains the fragments that will be displayed to the user.
The first fragment contains just a list of elements, and it is just an instance of the ListFragment class.
What I wanted to achieve was to replace the list fragment with another fragment that contains the details of the item that has been clicked.

The first solution that came to my mind, and that didn't work, involved the creation of a simple interface with a method that gets an item as a parameter. The item is the one the user clicked on.

{% highlight java %}
public interface OnItemSelected extends Serializable {
    void itemSelected(Item item);
}
{% endhighlight %}

Later, I wanted to pass an object that implemented this interface as an argument to my list fragment, that would call the method when the user selected an item of the list.
The code looked like this:

{% highlight java %}
final FragmentManager fragmentManager = getFragmentManager();
final Fragment listFragment = MyListFragment.newInstance(new OnItemSelected() {
    @Override
    public void itemSelected(Item item) {
        final FragmentTransaction transaction = fragmentManager.beginTransaction();
        transaction.replace(R.id.fragment_container, DetailsFragment.newInstance(item));
        transaction.addToBackStack(null);
        transaction.commit();
    }
});

final Fragment container = fragmentManager.findFragmentById(R.id.fragment_container);
if (container == null) {
    final FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
    fragmentTransaction.add(R.id.fragment_container, listFragment);
    fragmentTransaction.commit();
}
{% endhighlight %}

The problem with this code is that the anonymous inner class that implements the interface I created before contains a reference to the activity to be able to access the FragmentManager when handling the click event. When I tried the application, I could see the list of the items. However, since the Activity class is not serializable, when I pressed the back button, what I got is this Exception:

{% highlight java %}
java.lang.RuntimeException: Parcelable encountered IOException writing serializable
object (name = package.MainActivity$1)
        at android.os.Parcel.writeSerializable(Parcel.java:1316)
        at android.os.Parcel.writeValue(Parcel.java:1264)
        at android.os.Parcel.writeArrayMapInternal(Parcel.java:618)
        at android.os.Bundle.writeToParcel(Bundle.java:1692)
        at android.os.Parcel.writeBundle(Parcel.java:636)
        at android.app.FragmentState.writeToParcel(Fragment.java:132)
        at android.os.Parcel.writeTypedArray(Parcel.java:1133)
        at android.app.FragmentManagerState.writeToParcel(FragmentManager.java:373)
        at android.os.Parcel.writeParcelable(Parcel.java:1285)
        at android.os.Parcel.writeValue(Parcel.java:1204)
        at android.os.Parcel.writeArrayMapInternal(Parcel.java:618)
        at android.os.Bundle.writeToParcel(Bundle.java:1692)
        at android.os.Parcel.writeBundle(Parcel.java:636)
        at android.app.ActivityManagerProxy.activityStopped
(ActivityManagerNative.java:2467)
        at android.app.ActivityThread$StopInfo.run(ActivityThread.java:3098)
        at android.os.Handler.handleCallback(Handler.java:733)
        at android.os.Handler.dispatchMessage(Handler.java:95)
        at android.os.Looper.loop(Looper.java:136)
        at android.app.ActivityThread.main(ActivityThread.java:5017)
        at java.lang.reflect.Method.invokeNative(Native Method)
        at java.lang.reflect.Method.invoke(Method.java:515)
        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run
(ZygoteInit.java:779)
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:595)
        at dalvik.system.NativeStart.main(Native Method)
 Caused by: java.io.NotSerializableException: package.MainActivity
        at java.io.ObjectOutputStream.writeNewObject(ObjectOutputStream.java:1364)
        at java.io.ObjectOutputStream.writeObjectInternal(ObjectOutputStream.java:1671)
        at java.io.ObjectOutputStream.writeObject(ObjectOutputStream.java:1517)
        at java.io.ObjectOutputStream.writeObject(ObjectOutputStream.java:1481)
        at java.io.ObjectOutputStream.writeFieldValues(ObjectOutputStream.java:979)
        at java.io.ObjectOutputStream.defaultWriteObject(ObjectOutputStream.java:368)
        ...
{% endhighlight %}

I got the solution to my problem by posting a [question](http://stackoverflow.com/questions/23529357/java-io-notserializableexception-when-clicking-back-button) in StackOverflow. Basically, the new approach involved making the fragment usable only inside an Activity that implements the OnItemSelected interface. And, for the last step, I changed the main activity to actually implement it.
Here are the details of the changes:

{% highlight java %}
@Override
public void onAttach(Activity activity) {
    super.onAttach(activity);
    if (activity instanceof OnSelectedItem) {
        onSelectedItemListener = (OnSelectedItem) activity;
    } else {
        throw new RuntimeException(
"The hosting activity must implement the OnSelectedItem interface.");
    }
}
{% endhighlight %}

{% highlight java %}
public class MainActivity implements OnItemSelected {

    ...

    @Override
    public void itemSelected(Item item) {
        final FragmentManager fragmentManager = getFragmentManager();
        final FragmentTransaction transaction = fragmentManager.beginTransaction();
        transaction.replace(R.id.fragment_container,
ItemDetailsFragment.newInstance(item));
        transaction.addToBackStack(null);
        transaction.commit();
    }
}
{% endhighlight %}

And that's it, now the application works as expected.

I hope this post can be useful. See you soon!
