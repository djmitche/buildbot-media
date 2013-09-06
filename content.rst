Why Buildbot
============

What purpose does Buildbot serve?
When would I want to use it?

Buildbot is a framework for automating build, test, and release processes.
It's used for continuous integration, continuous deployment, and release management.

Building
--------

Building means compiling, minifying, linking and generally producing what you need to actually run the app from the source code.
You want this process to be automated and repeatable, so that you can eliminate variation in the build process or environment as a cause of different behavior.
This is particularly important in CD - shipping an RPM accidentally built on the wrong version of CentOS to all of your production servers would be very painful.
At Mozilla, we build for lots and lots of platforms - more than we could expect any one developer to maintain - and each build can take a very long time.
So automation, and parallelism, is critical to getting results in a reasonable (but still very long) amount of time.
Open source projects generally support multiple operating systems or environments, and more often those platforms are maintained by one or two project members.
Automating builds on those platforms allows everyone to be confident their change hasn't broken a platform they don't normally use.

Testing
-------

Testing means running test suites of all sorts - unit, acceptance, regression, integration.
Agile best practices tell us to integrate constantly and run tests all the time.
For a small project, developers can do this themselves, but new contributors usually won't.
Test suites get longer, and especially integration and acceptance tests tend to be slow.
Tests, even for platform-agnostic projects, tend to show failures on different platforms.
At Mozilla, we have a lot of tests, and a lot of platforms and builds to run them on.
The build and tests for a single commit can add up to hundreds of hours.
Obviously, then, parallelism is important!

Releasing
---------

Release processes include packaging software to ship to customers, and the deployment phase of continuous deployment.
Like building and testing, automating release processes gives predictability and repeatability.
Just like testing the software, performing releases frequently (even if the results aren't actually shipped) helps to tease out bugs that will otherwise affect customers or make schedules slip.
For open-source projects, automating the release process also tends to allow more releases: it's usually at least a few hours' work to ship a release by hand, so busy project maintainers tend to put off releases in favor of other work on the project.
At Mozilla, the release process involves a lot more than building RPMs and DMGs: tagging, localization, QA, signing, MAR generation, update snippets, and much more are all handled automatically.
This has drastically shortened the end-to-end time required to make a release, and reduced the chances for human error along the way.

Framework
---------

Buildbot aims to be a framework, which is somewhere between an application and a library.
Applications work out of the box, and you configure them until they do what you want.
Libraries provide utilities, but leave it up to you to write the core code.
A framework is somewhere in between: it takes care of all of the common tasks, while you write the code to handle the things that make your requirements unique.

Other Tools
-----------

Buildbot is by no means the only tool in this space.

I'll talk a little about the other options here, but my focus is on Buildbot itself, so I don't spend a lot of time making comparisons.
If other tools suit a particular purpose better than Buildbot, then by all means use them.

Tinderbox
.........

Long-time Mozillians may remember Tinderbox; some even fondly!
Tinderbox uses a "push" model, and takes the "continous" in continuous integration literally.
Each host builds the software in a loop, updating to the most recent version at the beginning of each iteration.
The hosts email the logs to a central tinderbox server for display.
Thus the central server has no way to control what gets built -- or what does not.

Jenkins
.......

Jenkins, formerly Hudson, is a very popular open source, web-based CI application.
It supports configuration via the web interface, and by default performs tasks on a single host.
It is massively extensible with a wide variety of plugins, although the system is Java so it's not trivial to write or modify such plugins.
As such, if you have relatively simple requirements that are supported by Jenkins and its plugins, then Jenkins is very easy to set up.
However, the difficulty increases quickly for unusual or complex requirements.

Travis-CI
.........

Travis is a free, cloud-based build service that integrates with Github.
It's designed around building commits and pull requests and providing simple pass/fail responses.
It supports a number of languages and types of projects, but is fairly limited in the sorts of build environments it can provide.
For example, it works great with Gems, Python libraries, and JavaScript libraries, but provides only one build environment for C/C++ projects, without any of the dependencies you might need.
This is a great gateway-drug CI system for small side projects that would otherwise have no CI infrastructure.

Buildbot's Concepts
===================

For any application, it's important to understand the underlying conceptual model to use the tool effectively.
As a framework, Buildbot brings a certain conceptual model, but allows users to customize the pieces of that model to suit the requirements

At its core, Buildbot is a job scheduling system: it queues jobs (called *builds* for historical reasons), executes the jobs when the required resources are available, and reports the results.
So there are three pieces to consider: generating new jobs, executing jobs, and reporting on the jobs' results.

Generating Jobs
---------------

Buildbot's *change sources* watch for commits (called *changes*).
The built-in change sources support the most common version-control systems with flexible configuration to support a variety of deployments.
*Schedulers* react to new data from change sources, external events, or triggers based on clock time (e.g., for a nightly build), and add new *build requests* to the queue.

Executing Jobs
--------------

Each build request comes with a set of source stamps identifying the code from each codebase used for a build.
A *source stamp* represents a particular revision and branch in a source code repository.
Build requests also specify a *builder* that should perform the task and a set of *properties*, arbitrary key-value pairs giving further detail about the requested build.
Each *builder* defines the steps to take in executing a particular type of build.
Once the request is selected for execution, the steps are performed sequentially.
Each build is executed on exactly one slave.

Reporting
---------

Once a build is complete, the framework reports the results---log files and step status---to users and developers via *status listeners*.
Buildbot has built-in support for reporting via web, email, irc, Tinderbox, and gerrit.

Buildbot has a distributed master-slave architecture where the *masters* instruct the *slaves* to perform each step of a build in sequence.
The slave portion of Buildbot is platform-independent, and many slaves can be connected to each master, running builds in parallel.
A slave may execute multiple builds simultaneously, as long as they are for different builders.

The Buildbot master and slave are both implemented as Python daemons.
Small Buildbot installations are generally composed of one master and tens of slaves while larger installations run tens of masters with hundreds or thousands of slaves.

Using Buildbot
==============

TODO

Running Buildbot
================

TODO
