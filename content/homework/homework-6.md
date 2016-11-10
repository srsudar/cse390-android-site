+++
date = "2016-11-10T11:55:37-08:00"
title = "Homework 6"

+++

# tl;dr

1. Send me an email addressing the three bullet points below in the Dependency
   Hell section.
     1. Why are you getting that error
     1. What libraries are causing the problem
     1. How would you fix it?

1. Send me an email with a link to your repo test class for a modal view like I
   describe below. If you are doing your own app, send me a file with tests.
   They should pass.


# Long Form

This week the plan is to write some Robolectric tests. If you're creating your
own app (as opposed to creating the Github repo browser like I am doing), you
should write tests that make sense for your app.

[Robolectric](https://github.com/robolectric/robolectric) lets us write unit
tests that don't depend on the Android framework. Normally, Android code can
only run on an actual device. This is because, at compile time, the Android
java code comes from the SDK. The `.class` files in the SDK, however, like the
`Activity.class` file you reference when you `import android.app.Activity;`, do
not have real implementations. They have the same signatures (i.e. the same
methods), but the implementations all are `throw new
RuntimeException("Stub!");`. This means that you can't actually run the java
code that you compile if you add this code to your runtime classpath. When you
run the code on a device, this isn't a problem, as the Android `.class` files
are provided by the Android device itself on the run time classpath.

Robolectric changes this. It provides real implementations of the Android
`.class` files, allowing us to run code on the Java Virtual Machine (JVM),
rather than the Dalvik VM that runs Android code on the device. If this sounds
complicated and confusing, it is. No need to worry if it doesn't make complete
sense right away. The benefit to running it on the JVM is that you don't need
to pull out your phone, hook it up, and wait for the APK to install just to run
a simple unit test. The other benefit is that Robolectric wraps some of the
Android code, allowing you acess to variables and objects that Android doesn't
expose. This lets you write more meaningful assertions (in some cases).
Robolectric is widely used for Android unit tests. It isn't the only option,
but it is very commonly used.



# Tasks

## 1. Dependency Hell

First we're look at one of the build issues I talked about in class. My version
of the Github repo browser is located at
[github.com/srsudar/GithubHotness](https://github.com/srsudar/GithubHotness).
Clone this repo and pull the tags, then check out the tag
`guava-version-mismatch`:

```shell
# From within the cloned repo
git pull origin --tags
git checkout guava-version-mismatch
```

You'll be in a detached HEAD state, but that's ok. **All of the following
commands assume you are in the top level of the repo (the one with the `.git/`
directory).**

First, let's try running the tests from the command line, rather than from
Android Studio. This corresponds to a gradle task named `testDebugUnitTest`.
If you have gradle installed, you can run `gradle :app:testDebugUnitTest`.
However, this isn't a perfect solution. For one, recall one of my primary
complaints about Eclipse in class--it doesn't readily give you a way to perform
clean, repeatable builds in a way that is separate from the UI. The command
above is similarly not ideal. It might work, but it won't be completely
repeatable. Relying on the installed version of gradle is the problem
here--what if collaborators use a different gradle version or different
settings? Maybe the builds will be subtly different in a way that is hard to
reproduce or leads to tricky bugs.

To circumvent this, gradle typically ships with the so-called 'gradle wrapper'.
(This isn't the only benefit of using the wrapper, but it is a plus.) The
gradle wrapper is the file named `gradlew` in the directory. It has executable
permissions and can be used as a replacement for the bare gradle command. So
instead of `gradle :app:testDebugUnitTest`, you should run `./gradlew
:app:testDebugUnitTest`. The `:app` portion of the command tells gradle to look
in the app directory (or at the `app` target), and the `:testDebugUnitTest`
tells it to run the task called `testDebugUnitTest`. If you type `./gradlew
:app:tasks`, you will see a list of all the tasks in the `app` target that you
can run.

Run `./gradlew :app:testDebugUnitTest`. If you've checked out the
`guava-version-mismatch` tag, you should see an error, complaining that there
is a mismatch with a dependency called guava.

After you find that, run `./gradlew :app:dependencies`. This will show you your
complete dependency tree for the various targets in the `app` module.

Send me an email with the following:

1. What is the meaning of the error that you are seeing when you try the
   `testDebugUnitTest` task? What is going on, and why is that a problem?
   Google it if it isn't obvious.
1. What libraries are causing the conflict?
1. What are some potential ways to fix this? This is the hardest part. You can
   look at the `master` branch to see what I eventually did.

# 2. Write Robolectric Tests for a Modal RecyclerView

The real bulk of the assignment this week is going to be writing a small view,
and writing tests to go along with that view. The idea here is that a
RecyclerView (which you implemented over the past two weeks), is a great way to
display lists of items. However, when you display lists of items, you generally
need to handle a few other cases as well. For example, what if there are no
items to display? The default behavior is just to display nothing. That isn't
ideal, as the user won't know if there's just nothing to view, if something has
gone wrong, etc. I like to handle four potential states when I am writing a
view to display a list of items:

* Loaded: Everything worked and you've loaded some items. This will just
    display the RecyclerView.
* Empty: Everything worked, but there are no items to display. This will
    probably just be a `TextView` saying something like 'Nothing to see here.'
* Error: There was an error somewhere along the line. You can update this with
    specifics of the error, but the minimum thing you'll need is a message
    saying something like 'Whoops, something went wrong'.
* Loading: This is a spinning icon or a loading bar to show that work is being
    done. One candidate here is some sort of progress bar set to indeterminate
    progress status. In my example I'm using a
    [`SwipeRefreshLayout`](https://developer.android.com/reference/android/support/v4/widget/SwipeRefreshLayout.html)
    to give a 'pull down to refresh action', so my loading view is just to set
    this to refresh initiated. When a load completes, I call the view to
    indicate that the load has completed, hiding the little spinning icon.

So, the idea here is to write a view that has these four modes: Loaded, Empty,
Error, and Loading. When I did this, I defined an `enum` with those four
choices, subclassed `LinearLayout`, and created a method called
`updateViewState(ViewState viewState)`. This is responsible for hiding all the
irrelevant views and showing only the view that matches `viewState`.

As you implement that class, you should write Robolectric tests to ensure that
you are showing and hiding the views as appropriate. **To submit the
assignment, send me links to the class holding your tests.**

Here is some starter code (that won't run or build), that you can use for an
idea of what I'm looking for. The full files are in the
[repo](https://github.com/srsudar/GithubHotness) if you get stuck. Note that
because I'm using Dagger2 for dependency injection, the way I'm creating
objects for member variables may look unfamiliar.

My `RepoListView` class:

```java
public class RepoListView extends LinearLayout {
  public enum ViewState {
    LOADED, EMPTY, ERROR, LOADING
  }

  RecyclerView rvItems; // Your RecyclerView, which will display the items
  TextView tvEmpty;     // Contains the message for empty
  TextView tvError;     // Contains the message for an error
  TextView tvLoading;   // Contains a message for loading

  public RepoListView(Context context, AttributeSet attrs) {
    super(context, attrs);
    init(context);
  }

  public RepoListView(Context context, AttributeSet attrs, int defStyleAttr) {
    super(context, attrs, defStyleAttr);
    init(context);
  }

  private void init(Context context) {
    // You'll need to define a view in layout/ that has the appropriate
    // children views.
    LayoutInflater.from(context).inflate(R.layout.view_repo_list, this, true);

    // All the findViewById calls, setting up the items, adapter, etc
  }

  public void updateViewState(ViewState state) {
    switch (state) {
      // Show and hide the views.

    }
  }

  // More methods as you need them. Take a look in my project if you want to
  // see how I did it, but try on your own first.
}
```

Your test file might then look like this:

```java
@RunWith(RobolectricTestRunner.class)
@Config(
  constants = BuildConfig.class,
  sdk = 22
)
public class RepoListViewTest {
  RepoListView view;

  @Before
  public void before() {
    // This method is run before each test.
    LayoutInflater inflater =
        LayoutInflater.from(RuntimeEnvironment.application);
    view = inflater.inflate(R.layout.inflatable_repo_list_view);

    // Set up mocks here. Remember that you want to test ONLY RepoListView
    // functionality here, not any dependency code. You might do something
    // like:
    view.adapter = mock(MyAdapter.class);
    // etc
  }

  @Test
  public void updateViewState_correctForLoaded() {
    // Test code here. I have lines like the following.
    // To get these great assertions, I have this line in my build.gradle:
    // testCompile 'com.squareup.assertj:assertj-android:1.1.1'
    // If that gives you build errors, look at my whole file here and see if
    // this 'exclude' option fixes it for you:
    // https://github.com/srsudar/GithubHotness/blob/master/app/build.gradle
    ...
    assertThat(view.tvError).isGone();
    ...
  }

  @Test
  public void updateViewState_correctForLoading() {

  }

  @Test
  public void updateViewState_correctForError() {

  }

  @Test
  public void updateViewState_correctForEmpty() {

  }
}
```
