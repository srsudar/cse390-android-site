+++
date = "2016-10-18T16:05:07-07:00"
title = "Homework 3"

+++

# tl;dr

Check out the branch `failing-tests-1` from the mdr-original project you built
for Homework 1. Many tests should fail. Fix both the tests in the `android` and
`androidTests` configurations. Some rules:

1. **DO NOT CHANGE THE TESTS**: You should not have to edit any test code to
   get these to pass. All changes should be made in non-test Java files and in
   xml files. If you think you found a reason you need to change test code, and
   you REALLY REALLY REALLY think you're right, email me and ask.
1. **DO NOT LOOK AT THE DIFF**: This is a git repo. The nature of version
   control allows you to fix all the tests via nothing more than git trickery.
   This would be super lame, however. Android is something where you learn by
   doing. If you cheat, you're only hurting yourself. Further, these
   assignments are largely optional. You could just not do it and save the
   effort you think you're saving by using the diff. If you're taking the time
   to revert the diff and find yourself feeling clever, that feeling might be
   misplaced.

# Backstory

An incompetent collaborator has gotten ahold of the
[mdr-original](https://github.com/srsudar/mdr-original) project. Thinking
themselves brilliant, they've made loads of edits but never run the tests. 'I
am god's gift to Android programming', you imagine them saying as they type
`git push`.

Being the selfless hero that you are, it falls to you to save the project from
incompetence. You clench your jaw, imagine yourself on a mountain before a flag
waving triumphantly in the wind, and with a steely gaze you get to work.

# The 'How'

The broken tests are on the branch called `failing-tests-1`. Assuming you are
using `git` from the command line, you would run this:

```shell
git fetch
git checkout failing-tests-1
git branch # should output '*failing-tests-1' or similar
```

Open the code in Android Studio. You should encounter a build error complaining
about a tool not found (unless you'd installed it previously).  This was
intentional. >:) In the real world you'll have to fix a lot of these types of
errors. Better to get comfortable with them when there's someone you can ask
(me).

There are two types of tests in Android Studio: `test` and `androidTest`.
`test` are unit tests that can run on the JVM. In this case we've configured
them to use Robolectric (more on this next week). `androidTest` are tests that
need to run on an actual device. To run these you'll need either a physical
device or an emulator.

Right click on the `android` tests and click 'Run Tests'. **29 tests should
fail**. Your job is to get them passing. Fixing one bug might fix more than one
tests, so it's not as bad as it may seem at first.

Right click on the `androidTest` tests and click 'Run Tests'. **1 test should
fail**. You need an emulator or physical device for this one.

Look at the logs for the errors. This will be helpful in many cases, and will
tell you were to look. Most of these tests have to do with Android UI (e.g.
`View`s), and one or two may pertain to the lifecycle.

You should **not need to edit the tests for any of these to pass**. If you
think you do, email me and I will tell you if I've made a mistake. I'll forward
your email to the list, giving you credit. Tears of joy will be shed, glory
will be basked in. Life will be good. (But seriously, I don't think you'll need
to edit any tests.)

You should also **not look at the diff**. I could have done this in a way that
didn't give you access to the git history. I didn't think it was worth the
effort. If you're cheating, you're only hurting yourself.

> See the tl;dr section for more on the rules.

Don't forget that UIs are generally specified two places:

1. An xml file, e.g. `res/layout/activity_main.xml`.
1. A Java Activity, e.g. `org/mamasdelrio/android/MainActivity.java`.

You'll have to edit more than these files, but those are good places to start.

If you get stuck, [ask questions on this thread on the message
board](https://catalyst.uw.edu/gopost/conversation/sudars/974700). If that
doesn't work or you don't feel comfortable posting there, I'm also happy to
field emails.

# The 'Why'

These tests try to give you more exposure to UI, `View`s, and `Activity`s. Next
week you'll have to create UIs (with tests!) from scratch yourself. Use this to
get accustomed to how everything works together.


# Submitting

Fork the repo on github and submit your changes to a `failing-tests-1` branch
there. [Here is a guide on github
forking](https://help.github.com/articles/fork-a-repo/). Email me the link with
'HW 3: Submission' in the subject.

I recognize this is a bit of a git-heavy and github-heavy class if you're not
familiar with these tools. If you find this overwhelming, email me and I will
try to help you understand them. These are things that you'll be better off
knowing, and I am consciously trying to encourage that you become comfortable
with them. If you **really** don't want to use github, you can email me a patch
file with the same subject as above.
