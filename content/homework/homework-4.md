+++
date = "2016-10-25T17:27:51-07:00"
title = "Homework 4"

+++

# tl;dr

Implement your own `RecyclerView` to match the images shown below. Pressing the
Floating Action Button (FAB) adds an item. Pressing the trashcan deletes the
item. Adds and deletes are animated.


# tl; but will still r

As we talked about today,
[`RecyclerView`](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.html)
is a class that facilitates displaying lists of items, and it does so
efficiently. To accomplish this it enforces the ViewHolder design pattern in
order to minimize the number of `findViewbyId()` calls, as these calls traverse
the view hierarchy and can be very expensive. `RecyclerView` also provides a
number of other benefits, including fine-grained control of child positioning,
via the
[`LayoutManager`](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.LayoutManager.html),
and painless animations thanks to
[`ItemAnimator`](https://developer.android.com/reference/android/support/v7/widget/RecyclerView.ItemAnimator.html).

**Homework 4 is to implement a `RecyclerView` that looks like the images shown
below**.

I would break this into several steps. These are somewhat specific, as many
people missed last week's lecture and I get the impression that the way all of
this plumbs together remains somewhat opaque. Much of this is described in
Android's guide to [Creating Lists and
Cards](https://developer.android.com/training/material/lists-cards.html), so
take a read through that as well.

1. **Create a new Activity**: This `Activity` will hold the `RecyclerView`. To
   get a lot of the annoying boilerplate out of the way, I would recommend
   using File | New | Activity | Gallery, and then selecting 'Basic Activity'.
   This should include a layout and a 'Floating Action Button', which is a
   common design element on Android.
1. **Download the icons**: You'll need two icons for this: a trashcan and a
   white plus button to go in the FAB. Download the icons
   [here](https://design.google.com/icons/). They are called `delete` and
   `add`. Download the PNGs and put the `drawable-*` directories directly under
   `res/`. You can try to use an icon importer, but I've never gotten this to
   work. When successful, you should be able to refer to the drawables by their
   name, e.g. `@drawable/ic_add_white_24dp` in your xml.
1. **Include RecyclerView in a layout**: As part of the generation process in
   step 1, Android Studio will generate some layout files for you. These files
   live in `res/layout/`, and define what your view hierarchy will look like.
   Note that in your `Activity`'s `onCreate()` method, the wizard has called
   `setContentView()` with the name of your layout file. This is where we
   connect the layout in the xml to the UI presented by your `Activity`. Define
   a `RecyclerView` in that layout file, giving it an ID so that you can refer
   to it from you `Activity` code. The Creating Lists and Cards guide above has
   examples of this.
1. **Create a dummy object to display**: `RecyclerView` displays lists of
   objects. In the real world, this would be an object representing something,
   like a Github repo or an email. You can create whatever object you want for
   this. I made one called `FooObject`, the entirety of which looks like this:
   ```java
   public class FooObject {
     public String name;
   }
   ```

     To create these objects I also made a `FooFactory`:

     ```java
     public class FooFactory {
       public FooObject createFoo() {
         Random random = new Random();
         int semiRandomInt = random.nextInt(500);
         FooObject result = new FooObject();
         result.name = "I am semi-random Foo #" + semiRandomInt;
         return result;
       }
     }
     ```
1. **Define a View to represent FooObject**: Adapters create Views. In our
   case, we want to do this by inflating an xml file living under
   `res/layout/`. You should make yours look like the image below. Note that
   there are two lines. The second line has two items: a left-aligned piece of
   text and a right-aligned image of a trashcan. I used two
   [`LinearLayout`](https://developer.android.com/guide/topics/ui/layout/linear.html)s,
   two
   [`TextView`](https://developer.android.com/reference/android/widget/TextView.html)s,
   and an
   [`ImageView`](https://developer.android.com/reference/android/widget/ImageView.html).
   I wrapped everything in a
   [`CardView`](https://developer.android.com/reference/android/support/v7/widget/CardView.html)
   to make it look fancy.
   ![Closeup of Item View]({{% baseurl %}}/homework/hw4_fooView.png)
1. **Create an Adapter**: Extend `RecyclerView.Adapter` and implement it to
   display your `FooObject`s. Again, the guide linked above will be extremely
   helpful here.
1. **Hook up the Activity**: Follow the guide above to implement your
   `onCreate()` method. You should now be able to run the app and find an empty
   screen. Pressing the FAB will generate a little message.
1. **Hook up the FAB**: Add a `View.OnClickListener` to the FAB to add a
   `FooObject` with every click.
1. **Hook up the delete button**: Add a `View.OnClickListener` to the
   `ImageView` displaying the trash can that deletes the view from the
   `Adapter`. To do this, I had to employ the `ViewHolder#getAdapterPosition()`
   method. I also used `View#setTag()`. This is probably the trickiest part, or
   at least it was the most foreign to me. Deleting the item from the dataset
   is relatively simple, but getting the listener in a scope where you can call
   `getAdapterPosition()` might not be immediately obvious.
1. **Hook up animations**: Make sure you are calling `notifyItemInserted()` and
   `notifyItemRemoved()` when you add and remove items. This will animate the
   actions and make them look pretty.

# Images

## Closeup of the Item View
![Closeup of Item View]({{% baseurl %}}/homework/hw4_fooView.png)

## Screenshot of the Main Activity
![Screenshot]({{% baseurl %}}/homework/hw4_screenshot.png)
