---
title: "Why use a Cluster?"
teaching: 15
exercises: 5
questions:
- "Why would I be interested in High Performance Computing (HPC)?"
- "What can I expect to learn from this course?"
objectives:
- "Describe what an HPC system is"
- "Identify how an HPC system could benefit you."
keypoints:
- "High Performance Computing (HPC) typically involves connecting to very large
  computing systems elsewhere in the world."
- "These other systems can be used to do work that would either be impossible
  or much slower on smaller systems."
- "HPC resources are shared by multiple users."
- "The standard method of interacting with such systems is via a command line
  interface."
---
<br>

Frequently, research problems that use computing can outgrow the capabilities
of the desktop or laptop computer where they started:

<br>

<div class="row">
  <div class="col-sm-6 col-md-4">
    <div class="thumbnail">
      <img src="/hpc-intro/fig/dna-solid.svg" width="30%" height="30%" alt="...">
      <div class="caption">
        <h3>Genomics</h3>
        <p>A genomics researcher has been using small datasets of sequence data,
           but soon will be receiving a new type of sequencing data that is 10
           times as large. It's already challenging to open the datasets on a
           computer -- analyzing these larger datasets will probably crash it. In
           this research problem, the calculations required might be impossible to
           parallelize, but a computer with <b>more memory</b> would be required to
           analyze the much larger future data set.</p>
      </div>
    </div>
  </div>

  <div class="col-sm-6 col-md-4">
    <div class="thumbnail">
      <img src="/hpc-intro/fig/cubes-solid.svg" width="38%" height="38%" alt="...">
      <div class="caption">
        <h3>Engineering</h3>
        <p>An engineer is using a fluid dynamics package that has an option to
           run in parallel. In this research problem, the calculations in each region
           of the simulation are largely independent of calculations in other regions
           of the simulation. It's possible to run each region's calculations
           simultaneously (in <b>parallel</b>), communicate selected results to
           adjacent regions as needed, and repeat the calculations to converge on a
           final set of results.</p>
      </div>
    </div>
  </div>

  <div class="col-sm-6 col-md-4">
    <div class="thumbnail">
      <img src="/hpc-intro/fig/book-open-solid.svg" width="38%" height="38%" alt="...">
      <div class="caption">
        <h3>Humanities</h3>
        <p>A graduate student is using a named entity recognizer to identify named
        entities (important people, places, and things) in the works of Ralph Waldo
        Emerson. In this research problem, each of Emerson's works are independent
        of each and can be analyzed simultaneously in <b>parallel</b>. Results from
        this retrieval task can be aggregated for higher level analyses such as
        knowledge graphing, mapping references, or social network analysis.</p>
      </div>
    </div>
  </div>
</div>

In all these cases, access to more (and larger) computers is needed. Those
computers should be usable at the same time, __solving many researchers'
problems in parallel__.

## Jargon Busting Presentation

Open the [HPC Jargon Buster]({{ site.url }}{{ site.baseurl }}/files/jargon.html#p1)
in a new tab. To present the content, press `C` to open a **c**lone in a
separate window, then press `P` to toggle **p**resentation mode.

> ## I've Never Used a Server, Have I?
>
> Take a minute and think about which of your daily interactions with a
> computer may require a remote server or even cluster to provide you with
> results.
>
> > ## Some Ideas
> >
> > * Checking email: your computer (possibly in your pocket) contacts a remote
> >   machine, authenticates, and downloads a list of new messages; it also
> >   uploads changes to message status, such as whether you read, marked as
> >   junk, or deleted the message. Since yours is not the only account, the
> >   mail server is probably one of many in a data center.
> > * Searching for a phrase online involves comparing your search term against
> >   a massive database of all known sites, looking for matches. This "query"
> >   operation can be straightforward, but building that database is a
> >   [monumental task][mapreduce]! Servers are involved at every step.
> > * Searching for directions on a mapping website involves connecting your
> >   (A) starting and (B) end points by [traversing a graph][dijkstra] in
> >   search of the "shortest" path by distance, time, expense, or another
> >   metric. Converting a map into the right form is relatively simple, but
> >   calculating all the possible routes between A and B is expensive.
> >
> > Checking email could be serial: your machine connects to one server and
> > exchanges data. Searching by querying the database for your search term (or
> > endpoints) could also be serial, in that one machine receives your query
> > and returns the result. However, assembling and storing the full database
> > is far beyond the capability of any one machine. Therefore, these functions
> > are served in parallel by a large, ["hyperscale"][hyperscale] collection of
> > servers working together.
> {: .solution}
{: .challenge }

{% include links.md %}

[dijkstra]: https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm
[hyperscale]: https://en.wikipedia.org/wiki/Hyperscale_computing
[mapreduce]: https://en.wikipedia.org/wiki/MapReduce
