+++
date = "2016-11-01T14:02:23-07:00"
title = "Week 5"

+++

# Recap and Slides

Today we talked about designing an app. Here are the slides:

* [Lecture 5: Designing an
    App](https://docs.google.com/presentation/d/1JbBoJ7OQ-DTXnoQ1DlfMFtqVEUA4J6OXE5cZsfP1gwY/edit?usp=sharing)

One of the questions that came up was how does Mockito do its magic. The
specific point was in an example like this one:

```java
Random mockRandom = mock(Random.class);
int nextInt = 5;
when(mockRandom.nextInt()).thenReturn(nextInt);
```

How does Mockito take an int and apply `thenReturn()` successfully? In other
words, say that you break down the above example to the following. How crazy
does this look, when really this is exactly what you are doing?

```java
Random mockRandom = mock(Random.class);
int nextInt = 5;
int originalAnswer = mockRandom.nextInt();
when(originalAnswer).thenReturn(nextInt);
```

I.e. you are basically saying `when(5).thenReturn(nextInt)`, which makes no
sense at all unless you consider the lines around it. I believe the way this
works is that when you call a method on a mock, it obeys previously determined
behavior (e.g. it returns the value you told it to return), **or** it flags
that stubbing has begun, and it will error if stubbing has not completed. Two
examples. The code above, with the `when(5)` example, does succeed and behaves
as expected, as the first example. This code, however, will fail with a
`MissingMethodInvocationException` error. Clearly Mockito is tracking your
stubbing progress, since you haven't called any methods on the mock yet.

```java
Random mockRandom = mock(Random.class);
int nextInt = 5;
int originalAnswer = 5;
when(originalAnswer).thenReturn(nextInt);
// Crash due to MissingMethodInvocationException
```

This code, meanwhile, will fail since we've begun stubbing with an invocation
of a method on the mock class (the call to `mockRandom.nextInt()`) and called
`when()`, but we haven't called `thenReturn()` or similar. It will fail with an
`UnfinishedStubbingException`.

```java
Random mockRandom = mock(Random.class);
int nextInt = 5;
int originalAnswer = mockRandom.nextInt();
when(originalAnswer);
// Crash due to UnfinishedStubbingException
```

So, calling a method on a mock seems to flag that, if stubbing is initiated
with a call to `when()`, the last called method is the one we want to save an
answer for. Once that is begun via the call to `when()`, it must be completed
via a call to `thenReturn()` or a similar method.

If you want to dive real deep down the rabbit hole, take a look at the
[implementation](https://github.com/mockito/mockito/blob/release/2.x/src/main/java/org/mockito/internal/MockitoCore.java#L68)
of the `when` method on github.

# Links

* [MVP on Android with Great
    Examples](http://engineering.remind.com/android-code-that-scales/)
* [More MVP on Android](http://antonioleiva.com/mvp-android/)
* [MVP in
    General](http://stackoverflow.com/questions/1317693/what-is-model-view-presenter)

