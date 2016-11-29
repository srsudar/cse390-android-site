+++
date = "2016-11-29T13:30:52-08:00"
title = "Homework 9"

+++

# tl;dr

Implement a `PreferenceAccessor` object to hide the complexities of dealing
with `SharedPreferences` every time you want to get a value. A single getter
and a single test is sufficient. Send me a link to your repo.


# Long with Lots of Detail

This week your task is to build something that saves data to disk. If you're
creating your own app, it can be whatever you need for your use-case. For
people following along with the Github Hotness application, we'll be making a
`PreferenceAccessor`.

[`SharedPreferences`](https://developer.android.com/reference/android/content/SharedPreferences.html)
are used to store key-value pairs on Android. A potential problem with
`SharedPreferences` is that you need your keys to be constant strings. If you
are accessing one of your key-values in various places around your app, you can
wind up with similar code all over the place. This is a no-no. Instead, I like
to bundle up this functionality in an object that I call an accessor.  This
`PreferenceAccessor` will be responsible for all the getting and setting of
your key values.

This is complicated somewhat by the fact that many of the `SharedPreferences`
methods are `static`. `static` things are hard to test, since many mocking
frameworks (including Mockito) cannot mock them out. This means it can be hard
to inject behavior/state into tests if they rely on `static` fields and
methods.

Luckily for us, this can be easily overcome with a level of indirection. Full
examples are in the
[slides](https://docs.google.com/presentation/d/1lNyuhuuRNBCjilPvZuGSQ_hQTjVrSpmKH5-ztzAckgQ/edit?usp=sharing),
but I'm not including that here because it would be too easy to just copy and
paste. Instead I am providing a similar example.

Imagine we have the following class:

```java
public class VendingMachineQuerier {
  public int getNumberCandyBars(String manufacturer) {
    VendingMachine machine = MachineFactory.getVendingMachine();
    return machine.getNumberForManufacture(manufacturer);
  }
}
```

We can use this class to get the number of candy bars in the vending machine
made by `Mars`, e.g.

But how can we test this? The standard way is to just pass in a
`VendingMachine` object, like:

```java
public class VendingMachineQuerier {
  public int getNumberCandyBars(String manufacturer, VendingMachine machine) {
    return machine.getNumberForManufacture(manufacturer);
  }
}
```

This would certainly work, and in many cases it is the right answer. Imagine
that for some reason you didn't want to do that. In the case of
`SharedPreferences`, e.g., I find it can be tedious on the caller and is better
centralized elsewhere.

We can instead create a package private method that returns a `VendingMachine`.
In the main code we'll have the real implementation, and in the test code we
will override that to provide a stub:

```java
public class VendingMachineQuerier {
  VendingMachine getVendingMachine() {
    return MachineFactory.getVendingMachine();
  }

  public int getNumberCandyBars(String manufacturer) {
    VendingMachine machine = getVendingMachine();
    return machine.getNumberForManufacture(manufacturer);
  }
}
```

In our test package, we then subclass `VendingMachineQuerier` and override this
getter method:

```java
public class TestVendingMachineQuerier {
  VendingMachine vendingMachineMock;

  public TestVendingMachineQuerier(VendingMachine machineMock) {
    this.vendingMachineMock = machineMock;
  }

  VendingMachine getVendingMachine() {
    return this.vendingMachineMock;
  }

  public int getNumberCandyBars(String manufacturer) {
    VendingMachine machine = getVendingMachine();
    return machine.getNumberForManufacture(manufacturer);
  }
}
```

Now we have a way to return a known object rather than the result of a static
factory call. Our tests will then be on this test object rather than the
original:

```java
public class VendingMachineQuerierTest {
  VendingMachineQuerier querier;
  @Mock
  VendingMachine machineMock;

  @Before
  public void before() {
    MockitoAnnotations.initMocks(this);
    
    // We'll now use our injectable subclass rather than the original:
    querier = new TestVendingMachineQuerier(machineMock);
  }

  @Test
  public void nameMeWell() {
    // Here we will interact with querier and have machineMock to perform
    // assertions.
  }
```
