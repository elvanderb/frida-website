---
layout: docs
title: Troubleshooting
prev_section: javascript-api
next_section: building
permalink: /docs/troubleshooting/
---

If you ever run into problems installing or using Frida, here's a few tips
that might be of help. If the problem you’re experiencing isn’t covered below,
please [report an issue]({{ site.repository }}/frida-website/issues/new) so the
Frida community can make everyone’s experience better.

## ValueError: ambiguous name; it matches:

This means the process name you specified in `frida.attach()` matches more than
one process. You can use the PID instead:

{% highlight py %}
session = frida.attach(12345)
{% endhighlight %}

## SystemError: attach_to_process PTRACE_ATTACH failed: 1

This (probably) means that you don't have permissions to attach to the target
process. The process may be owned by another user and you are not root. You may
have forgotten to enable ptrace of non-child processes. Try:

{% highlight bash %}
sudo sysctl kernel.yama.ptrace_scope=0
{% endhighlight %}

## Failed to spawn: unexpected error while spawning child process 'XXX' (task_for_pid returned '(os/kern) failure')

Under MacOS, this (probably) means that you don't have properly signed FRIDA or that there is some permissions missing (for example, you are running FRIDA under SSH).

If it's a signature problem, follow [this procedure]({{ site.repository }}/frida/frida#mac-and-ios) else, try:

{% highlight bash %}
**WARNING: This may weakens security**
sudo security authorizationdb write system.privilege.taskport allow
**WARNING: This may weakens security**
{% endhighlight %}

You also may have to disable System Integrity Protection to instrument system binaries, again : **WARNING: This WILL weakens security**).

## ImportError: dynamic module does not define init function (init_frida)

This or another similar error message is seen when trying to use `frida-python`
compiled for python 2.x in python 3.x, or vice versa. Check which python
interpreter you are running against which `PYTHONPATH` / `sys.path` is used.
