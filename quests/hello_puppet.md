{% include '/version.md' %}

# Hello Puppet

## Quest objectives

- Get started with the basics: What is Puppet and what does it do?
- Install the Puppet agent on a new system.
- Understand Puppet's resources and the resource abstraction layer.

## Get started

> Any sufficiently advanced technology is indistinguishable from magic.

> - Arthur C. Clarke

This quest kicks off your hands-on introduction to Puppet. We'll start by
installing the Puppet agent and learning some of the core ideas involved in
managing infrastructure with Puppet. Once the Puppet agent is installed on a
new system, you will use the `puppet resource` tool to explore the state of
that system. Through this tool, you will learn about *resources*, the
basic units that Puppet uses to describe and manage a system.

Ready to get started? Run the following command on your Learning VM to begin this quest:

    quest begin hello_puppet

## What is Puppet?

Before getting into the details of how Puppet works and how to use it, let's
discuss what Puppet is and why it's worth learning. When we
talk about Puppet, we're often using the name as a shorthand for a collection
of tools and services that work in concert to help you define how you want the
systems in your infrastructure configured, and then automate the process of bringing
those systems into your desired state and keeping them there. As you continue
with this guide, we'll dive deeper into Puppet's individual components,
but for now we'll introduce some of the key points that distinguish Puppet's
approach from the other tools you might use to manage your systems.

Puppet lets you to define a desired state for all systems in your
infrastructure. Once this state is defined, Puppet automates the process of
getting your systems into that state and keeping them there.

This state for your systems is written in Puppet code, a domain specific
language that lets you define how each server or device in your infrastructure
should be configured. This **infrastructure as code** approach means that users
concerned with version control, compliance, continuous integration and deployment,
and testing can easily fold infrastructure into their development and release workflow.

Puppet code is a **declarative** language, which means that you 
describe only the desired state for your systems, not the steps
needed to get there. Once you've specified in Puppet code those aspects of a
system you want Puppet to manage, a Puppet agent service running in the
background makes any changes needed to bring the system into compliance with
the desired state.

Puppet's declarative approach requires that you think about the systems in your
infrastructure differently than you would if you were using scripts, golden
images, or runbooks. Puppet has several advantages that help you spend time
focused on **what** your infrastructure can do for you rather than **how**
you're going to get there.

**Puppet is portable.** Its declarative language gives you a single syntax for
describing desired state across Windows and Unix-like operating systems,
network devices, and containers. You don't have to switch languages and toolsets
every time you start work on a new system. Learning Puppet gives you a skillset
that can be carried across projects and roles.

**Puppet is centralized.** With Puppet's master-agent architecture, there's no
need to connect to systems individually to make changes. Once the Puppet agent
service is running on a system, it will periodically establish a secure
connection to the Puppet master, fetch any Puppet code you've applied to it, and
make the changes necessary to bring the system in line with the desired state
you described. The fact that centralized control is built in from Puppet's
foundations makes monitoring and compliance much easier. Puppet also
offers orchestration features that allow you to coordinate changes across
multiple systems involved in an application.

**Puppet is community driven.** Joining the [Puppet
community](https://puppet.com/community) means you can benefit from the
knowledge and code contributed by thousands of other users around the world.
Ask questions on [Slack](https://slack.puppet.com/),
[IRC](http://webchat.freenode.net/?channels=puppet), or our
[Ask](http://ask.puppet.com/) Q&A site. Attend 
[Puppet User Groups (PUGs)](https://puppet.com/community/user-groups) to
connect with other Puppet users in your area. Get a jump start on your Puppet
infrastructure with [the Puppet Forge](forge.puppet.com), a repository of
modules maintained by Puppet and the Puppet community that give you everything
you need to manage common applications and services. The Forge has a wide range
of modules to help you manage everything from NTP and SQL Server to Minecraft.
The base of well tested and reviewed code means that you can get started
puppetizing key aspects of your infrastructure right out of the gate.

**Puppet connects you to the cutting edge.** Puppet provides a stable platform
for bringing new technologies into production. Integrations with Docker,
Kubernetes, Mesos, AWS, vSphere, and others help you engage with next generation
tools in a way that's simple, reliable, and consistent.

## The Puppet agent

As we noted above, what we call Puppet is actually a variety of tools and
services that work together to help you manage and coordinate the systems in
your infrastructure. Though this ecosystem gives you a great degree of power
and control, the complexity can leave a new user wondering where to start. So
this was the first question we answered as we put together this
guide: "Where do we begin?"

By introducing the Puppet agent and some of the command line tools included in
the agent installation, we hope to strike the right balance between the 
big-picture view of Puppet and the the bottom-up fundamentals. You'll begin to
understand the agent's role in the broader Puppet architecture, as well as the
details of how it interacts with the system where it's installed.

The Puppet agent runs on every system that you want Puppet to manage. The
agent serves as the bridge between the system where it's installed and the
Puppet master server. The agent communicates in two directions: out to the
master to see how its system should be configured, and then inward to native
system tools to check the actual state of the system and to make changes
to bring it in line with the desired state.

For now, we'll set aside the agent's interactions with the Puppet master 
and focus on the tools that Puppet uses to check and modify a system's state. When
you install the Puppet agent you get a set of command-line tools that help you
interact directly with a system in the same way the Puppet agent does. Using these
tools will help you understand how the Puppet agent sees and modifies the state
of the system where it's running.

## Agent installation

Though we're focusing on the local Puppet agent in this quest, we are
still working within the context of the master-agent architecture set up on the
Learning VM. The Learning VM itself has Puppet Enterprise
pre-installed, including the Puppet master service. For each quest, the quest
tool provides one or more agent systems that you explore and configure
with Puppet.

For this quest, we've prepared a fresh system where you can install the Puppet
agent and explore some of the tools it provides. The Puppet master hosts an
agent install script that makes it easy to install the Puppet agent on any
system that can access the master.

<div class = "lvm-task-number"><p>Task 3:</p></div>

To get started, use `ssh` to connect to `hello.puppet.vm` by running the following
command and entering the password `puppet`:

    ssh learning@hello.puppet.vm

Now that you're connected to this system, copy and run the following command to
load the agent installer from the master and run it on the agent system:

    curl -k https://learning.puppetlabs.vm:8140/packages/current/install.bash | sudo bash

You will see text stream across the screen as the installation runs.

If the script hangs due to an inability to access the required repositories,
it may be because network settings were changed after the agent was
created. Fix this by rebooting the VM and running
`quest begin hello_puppet` to restart this quest and re-create the agent
system.

In real life, if your agent is running on a different operating system than
the master, there are some additional steps to make the correct
installation script available. In this VM, our master and agent are both
running CentOS, so we don't have to worry about it. You can find
full documentation of the agent installation process, including specific
instructions for Windows and other operating systems in the [installation
documentation](https://docs.puppet.com/pe/latest/install_agents.html).

## Resources

As we noted above, the Puppet agent comes with a set of supporting tools you
can use to explore your system from Puppet's perspective. Now that the agent is
installed, let's use these tools to see what they can teach you about how
Puppet works.

One of Puppet's core concepts is the *resource abstraction layer*. For Puppet, 
each aspect of the system you want to manage (such as a
user, file, service, or package) is represented in Puppet code as a unit called
a *resource*. The `puppet resource` tool lets you view and modify these
resources directly.

<div class = "lvm-task-number"><p>Task 4:</p></div>

Be sure you're still connected your agent system, and run the following command
to ask Puppet to describe a file resource:

    sudo puppet resource file /tmp/test

What you see is the Puppet code representation of a resource. In this case,
the resource's type is `file`, and its path is
`/tmp/test`:

```puppet
file { '/tmp/test':
  ensure => 'absent',
}
```

Let's break down this resource syntax into its components so we
can see the anatomy of a resource a little more clearly.

```puppet
type { 'title':
   parameter => 'value',
}
```

The **type** is the kind of thing the resource describes. It can be a **core
type** like a user, file, service, or package, or a **custom type** that you
have implemented yourself or installed from a module. Custom types let you
describe resources that might be specific to a service or application (for
example, an Apache vhost or MySQL database) that Puppet doesn't know about out
of the box. A resource's type points Puppet to the set of tools it
will use to manage the resource on your system.

The body of a resource is a list of **parameter value pairs** that follow the
pattern `parameter => value`. The parameters and possible values vary from type
to type. They specify the state of the resource on the system. Documentation
for resource parameters is provided in the [Resource Type
Reference](https://docs.puppet.com/puppet/latest/reference/type.html).

The **resource title** is a unique name that Puppet uses to identify the
resource internally. In the case of our file resource that you looked at above, the
resource's title was the path of the file on the system: `/tmp/test`. Each
resource type has a unique identifying feature that can be used as the resource
title. A `user` resource uses the account name as a title, for example, and
a `package` resource uses the name of the package you want to manage.

There's an caveat to resource titles, however, that can trip up new users. You
can set a `file` resource's title to something other than the file's path 
if you also set a `path` parameter for that resource.  For example:

```puppet
file { 'my_file':
   ensure => 'present',
   path   => '/tmp/test'
}
```

A `file` resource's path will default to the title if the `path` parameter is
not explicilty set. The same pattern applies for other resources, such as a
user's account name or package name as we mentioned above. This parameter that
defaults to the title is called a **namevar**. You can find more information
about the **namevar** for different resource types in the [Resource Type
Reference](https://docs.puppet.com/puppet/latest/reference/type.html).

Now that you're more familiar with the resource syntax, let's take another look
at that file resource.

``` puppet
file { '/tmp/test':
  ensure => 'absent',
}
```

Notice that it has a single parameter value pair: `ensure => 'absent'`. The
`ensure` parameter tells us the basic state of a resource. For the *file* type,
this parameter will tell us if the file exists on the system and if it does,
whether it's a normal file, a directory, or a link.

<div class = "lvm-task-number"><p>Task 5:</p></div>

Puppet is telling us that this file doesn't exist. Let's see what happens when
you use the `touch` command to create a new empty file at that path. Run:

    touch /tmp/test

Now use the `puppet resource` tool to see how this change is represented in
Puppet's resource syntax:

    sudo puppet resource file /tmp/test

Now that the file exists on the system, Puppet has more to say about it. It
shows the `ensure` and `content` parameters and their values,
plus information about the file's owner, when it was created, and
when it was last modified.

``` puppet
file { '/tmp/test':
  ensure  => 'file',
  content => '{md5}d41d8cd98f00b204e9800998ecf8427e',
  ...
}
```

The value of the `content` parameter might not be what you expected. When the
`puppet resource` tool interprets a file in this resource declaration syntax,
it converts the content to an MD5 hash. This hashing lets Puppet quickly
compare the actual content of a file on your system against the expected
content to see if any change is necessary. This kind of shortcut means that
the way Puppet shows an existing resource on the system and the way that
resource would be fully defined in Puppet code's resource syntax can be a
little different.

<div class = "lvm-task-number"><p>Task 6:</p></div>

Let's use the `puppet resource` tool to add some content to our file.

Running `puppet resource` with a `parameter=value` argument tells Puppet to
modify the resource on the system to match the value you set. (Note that while
this is a great way to test out changes in a development environment, it's not
a good way to manage production infrastructure. Don't worry, though, we'll get
to that soon enough.) We can use this to set the content of your file resource.
Run the following command:

    sudo puppet resource file /tmp/test content='Hello Puppet!'

Puppet will display some output as it checks the hash of the existing content
against the new content you provided. When it sees that the hashes don't match,
it sets the file's content to the value of the command's `content` parameter.

Look at the file to see the modified content by running:

    cat /tmp/test

### Types and Providers

This translation back and forth between the state of the system and Puppet's
resource syntax is the heart of Puppet's resource abstraction layer. To get
a better understanding of how this works, let's take a look at another resource
type, the `package`.

<div class = "lvm-task-number"><p>Task 7:</p></div>

As an example, we'll look at the package for the Apache webserver `httpd`. Run:

    sudo puppet resource package httpd

Because this package doesn't exist on the system, Puppet shows you the
`ensure => purged` parameter value pair. This `purged` value is similar to the
`absent` value you saw earlier for the `file` resource, but in the case of the
package resource, indicates that both the package itself and any configuration
files installed by the package manager are both absent.

```puppet
package { 'httpd':
  ensure => 'purged',
}
```

As we mentioned above, each resource **type** has a set of **providers**. A
**provider** is the translation layer that sits between Puppet's resource
representation and the native system tools it uses to discover and modify the
underlying system state. Each resource type generally has a variety of
different providers.

These providers can seem invisible when everything is working correctly, but
it's important to understand how they interact with the underlying tools.

The quickest way to see the inner workings of a provider
is to break it. Tell Puppet to install a nonexistent package named
`bogus-package` by running:

    sudo puppet resource package bogus-package ensure=present

The error message tells you that yum wasn't able to find the
specified package, and lists the command that Puppet's yum provider tried to
run when it saw that the package wasn't already installed:

```
Error: Execution of '/bin/yum -d 0 -e 0 -y install bogus-package' returned 1:
Error Nothing to do
```

Puppet selects a default provider based on the agent's operating system and
whether the commands associated with that provider are available. You
can override this default by setting a resource's `provider` parameter.

Try installing the same fake package again, this time with the gem
provider:

     sudo puppet resource package bogus-package ensure=present provider=gem

You'll see a similar error with a failed gem command instead of the yum
command:

```
Error: Execution of '/bin/gem install --no-rdoc --no-ri bogus-package'
returned 2: ERROR:  Could not find a valid gem 'bogus-package' (>= 0) in any repository
Error: /Package[bogus-package]/ensure: change from absent to present failed: 
Execution of '/bin/gem install --no-rdoc --no-ri bogus-package' returned 2: 
ERROR:  Could not find a valid gem 'bogus-package' (>= 0) in any repository
```

Now that you know what's happening in the background, try installing
a real package with the default provider:

    sudo puppet resource package httpd ensure=present

This time, Puppet installs the package. The value of the
`ensure` parameter shows you the specific version of the installed package:

```puppet
package { 'httpd':
  ensure => '2.4.6-45.el7.centos',
}
```

When you don't specify a version of the package to install, Puppet
defaults to installing the latest available version and displays this version
number as the value of the `ensure` attribute.

## Review

In this quest, you learned what Puppet is and some of the advantages of
managing your infrastructure with Puppet's declarative domain-specific
language and master-agent architecture.

You installed the Puppet agent on a new system using an
install script hosted on the Puppet master. Once the agent and suite of
supporting tools were installed, you learned the fundamentals of Puppet's
**resource abstraction layer**, including **resources**, resource
**types**, and the **providers** that translate between your Puppet code and
the native system tools.

Now that you've learned some of the core concepts behind Puppet, you're ready
to use these ideas in a more realistic workflow. The `puppet
resource` command is a great way to explore a system or test Puppet code, but
it's not the tool you'll be using to automate your configuration management.

In the next quest, you'll learn how to save your Puppet code to a file called a
**manifest** and organize it into a **module** within your Puppet master's
**codedir**. This structure lets Puppet keep track of where to find all the
resources it needs to manage your infrastructure.

And you'll see how the Puppet agent communicates with your Puppet master to
fetch a compiled list of resources called a **catalog** based on Puppet code
kept on the master.

You'll learn how to save your Puppet code to a file called a **manifest** and
organize it into a **module** within your Puppet master's **codedir**. This
structure lets Puppet keep track of where to find all the resources it needs
to manage your infrastructure.

### Review

## Running the Puppet agent

The `puppet resource` and `facter` commands we explored above show you how
Puppet's resource abstraction layer works on your agent node. This tools are
useful for exploring the state of an unfamiliar system, but by themselves,
don't offer a considerable advantage over native commands or scripting. The
real benefit of Puppet's RAL is as an interface for centralization and
automation.

Let's walk through a Puppet agent run so you can understand how this system
works.

When you installed the Puppet agent, it set up a configuration file that tells
the agent node how to find and connect to the Puppet master. Until the Puppet
master knows to trust the Agent, however, you won't be able to complete a
Puppet agent run. To authenticate your agent node to the Puppet master, you
will have to sign its certificate. This allows SSL communication between the
Puppet master and agent nodes.

<div class = "lvm-task-number"><p>Task 12:</p></div>

Just to see what happens, try to trigger a puppet agent run without your agent
node's certificate signed. The agent will attempt to contact the Puppet master,
but its request will be rejected.

    puppet agent -t

You'll see a notification like the following:

    Exiting; no certificate found and waitforcert is disabled

No problem, you just have to sign the certificate. For now, we'll show you how
to do it from the command line, but if you prefer a GUI, the PE console
includes tools for certificate management.

<div class = "lvm-task-number"><p>Task 13:</p></div>

First, exit your SSH session to return to the your Puppet master node.

    exit

Use the `puppet cert` tool to list the unsigned certificates on your network.

    puppet cert list

Sign the cert for `hello.puppet.vm`.

    puppet cert sign hello.puppet.vm

<div class = "lvm-task-number"><p>Task 14:</p></div>

With that taken care of, the Puppet agent on your node will be able to contact
the Puppet master to get a compiled catalog of all the resources on the system
it needs to manage. By default, the Puppet agent will do this every thirty
minutes to automatically keep your infrastructure in line. Because it would be
difficult to demonstrate much with these scheduled background runs, however,
we have disabled these periodic agent runs. Instead, you will manually trigger
puppet runs so you can observe their effects.

Go ahead and reconnect to your agent node:

    ssh learning@hello.puppet.vm

Trigger another agent run. With your certificate signed, you'll see some
action.

    sudo puppet agent -t

While you haven't told Puppet to manage any resources on the system, you'll see
a lot of text scroll by. This process is called pluginsync. During pluginsync,
your agent checks to ensure that it has all the tools it needs to implement the
RAL and facter, and that the versions of these tools match what's on the
master.

This pluginsync process adds a lot of clutter. Without it, you'd see something
like the following.

```
Info: Using configured environment 'production'
Info: Retrieving pluginfacts
Info: Retrieving plugin
Info: Loading facts
Info: Caching catalog for hello.puppet.vm
Info: Applying configuration version '1464919481'
```

Though this output only shows a few of the things the Puppet agent is doing, it
gives you a few clues about the conversation between the agent and master. For
now, we'll focus on two key steps in this conversation.

First, the agent node sends a collection of facts to the Puppet master. These
are the same facts you see when you run the `facter` tool yourself. The Puppet
master takes these facts and uses them with your Puppet code to compile a
catalog. While Puppet code can contain language features such as conditionals
and variables, the catalog is the final parsed list of resources and parameters
that the Puppet master sends back to the agent node to be applied.

![image](../assets/SimpleDataFlow.png)

When you triggered a Puppet run, the Puppet master didn't find any Puppet code
to apply to your agent node, so it didn't make any changes. If we want to see
anything interesting happen, we'll have to tell the master what code we want
applied to our node.

<div class = "lvm-task-number"><p>Task 15:</p></div>

End your SSH session to return to the Puppet master:

    exit

Generally, you will organize all your Puppet code into classes and modules, but
just this once, we're going to make an exception and write some code directly
into the `site.pp` manifest. This manifest is one of the ways the Puppet master
determines what Puppet code should be applied to a node. Open the `site.pp`
manifest for the production environment.

    vim /etc/puppetlabs/code/environments/production/manifests/site.pp

First, we'll create block of code called a node delcaration.

```puppet
node hello.learning.puppetlabs.vm {
}
```

After the Puppet agent sends the facts from a node to the Puppet master, one of
the first step in compiling a catalog is to find a node block that matches the
agent node's fqdn fact. (We'll get into some more elaborate ways you can do
this step—such as using different facts and matching them against regular
expressions—in a later quest.)

With this node declaration in place, you can tell Puppet what resources you
want to manage on that node. For now, we'll use a resource type called `notify`
to output a message that we'll see as the Puppet run occurs and in Puppet's
logs. The notify resource type is a little different than most other resource
types in that it doesn't actually make any changes to the system. It can be
very useful, however, for testing and troubleshooting.

```puppet
node hello.learning.puppetlabs.vm {
  notify { 'Hello Puppet!':
    message => 'Hello Puppet!',
  }
}
```

(Note that while you'll learn the syntax more quickly if you type your code
manually, if you do want to paste content into vim, you can hit `ESC` to enter
command mode and type `:set paste` to disable the automatic formatting. Be sure
to press `i` to return to insert mode before pasting your text.)


Remember, use `ESC` then `:wq` to save and exit.

<div class = "lvm-task-number"><p>Task 16:</p></div>

It's always a good idea to check your code before you run it. 

    puppet parser validate /etc/puppetlabs/code/environments/production/manifests/site.pp

<div class = "lvm-task-number"><p>Task 17:</p></div>

Now SSH to your agent node:

    ssh root@hello.learning.puppetlabs.vm

And trigger another puppet run

    puppet agent -t

The output will include something like this:

    Notice: Hello Puppet!
    Notice: /Stage[main]/Main/Node[hello.learning.puppetlabs.vm]/Notify[Hello Puppet!]/message: defined 'message' as 'Hello Puppet!'
    Notice: Applied catalog in 0.45 seconds

Now that you've seen how to manage user accounts with the RAL and use the
`site.pp` manifest on your master to create node definitions, you're ready to
bring the two concepts together. In the next quest, you'll see how to create
a Puppet module to manage user accounts.

## Review

Nice work, you've finished the first quest on your way to Puppet mastery. At
this point you should have a general understanding of what Puppet is and some
of its core concepts and tools.

We covered the the installation of the Puppet agent on a new node using an
install script hosted on the Puppet master.

With that new node set up, you learned about the *resource abstraction layer*,
a key Puppet concept. You used `facter` to view system facts, and used the
`puppet resource` tool to investigate and modify the state of a `user`
resource.

We introduced the role of certificates in the master/agent relationship, and
the communication that takes place during a puppet agent run. Finally, you
defined a `notify` resource in the master's `site.pp` manifest and triggered
a puppet agent run on the agent node to enforce that configuration.
