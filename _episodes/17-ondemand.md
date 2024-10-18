---
title: "Open OnDemand"
teaching: 10
exercises: 10
questions:
- "How do we use Open OnDemand?"
objectives:
- "Open a shell session in your web browser"
- "Start an interactive Desktop session"
- "Start an interactive JupyterLab session"
keypoints:
- "Open OnDemand allows users to interface with an HPC system through a web browser."
---

[Open OnDemand] is an open-source web portal developed by Ohio Supercomputer Center
that enables researchers to access HPC systems from their web browsers. In the
previous section we showed how the {{ site.remote.name }} OnDemand File Manager can
be used to access, edit, and transfer data on an HPC filesystem. {{ site.remote.name }}
OnDemand also allows you to access a {{ site.remote.name }} shell, start an
interactive desktop session, and use interactive apps like JupyterLab, RStudio,
Matlab, and VS Code.

To access the {{ site.remote.name }} OnDemand Dashboard, open your web browser and
go to [http://ondemand.farmshare.stanford.edu].

## Access a {{ site.remote.name }} Shell

You can access a {{ site.remote.name }} by selecting **Clusters > {{ site.remote.name }}
Shell Access** from the top menu of the OnDemand Dashboard.

A new window will open in your browser, and you will automatically be logged onto
a {{ site.remote.name }} login node. This will work just like the shell session
in your local terminal app, but you don't need to log in with SSH or authenticate
with Duo Two-Factor authentication.

> ## X11 Forwarding
>
> X11 forwarding does not work with the OnDemand shell. If you need to use software
> with X11, you can continue to use your local terminal app or you can use an
> interactive desktop session.
{: .callout}

## Start an Interactive Desktop Session

The {{ site.remote.name }} Desktop session launches an interactive desktop on one
or more compute nodes, granting full access to the resources those nodes provide.
This is similar to an interactive job (`srun --pty bash`), with the added bonus of
a graphical user interface (GUI).

1. From the OnDemand Dashboard, select **Interactive Apps > {{ site.remote.name }}
Desktop**.
2. In the screen that opens, specify the **Size** (number of cores and memory),
whether to **Allocate a GPU**, and the **Number of hours** that the session should
run for.
3. Click the **Launch** button.
4. The **My Interactive Sessions** page will open. You will see a card for your
pending {{ site.remote.name }} Desktop session. Once the resources have been
allocated, you can click the **Launch FarmShare Desktop** button to open the
desktop session.

## Start an Interactive JupyterLab Session

{{ site.remote.name }} OnDemand allows users to launch common software GUIs in a web
browser, powered by the compute resources that you request. Currently available
interactive apps include **JupyterLab, MATLAB, RStudio, and VS Code.**

We will only cover how to start a JupyterLab session, but the process is largely the
same for the other apps.

1. From the OnDemand Dashboard, select **Interactive Apps > JupyterLab.**
2. In the screen that opens, specify the **Python version,** **Size**
(number of cores and memory), whether to **Allocate a GPU**, and the **Number of
hours** that the session should run for.
3. Click the **Launch** button.
4. The **My Interactive Sessions** page will open. You will see a card for your
pending JupyterLab session. Once the resources have been allocated, you can click
the **Connect to Jupyter** button to open the session.

{% include links.md %}

[Open OnDemand]: https://openondemand.org/
[http://ondemand.farmshare.stanford.edu]: http://ondemand.farmshare.stanford.edu/
