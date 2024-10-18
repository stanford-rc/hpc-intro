---
title: "Running a SLURM job array"
teaching: 30
exercises: 60
questions:
- "How do we execute a task in parallel?"
objectives:
- "Write a batch script for a single task."
- "Implement `--array` and `$SLURM_ARRAY_TASK_ID` in the batch script."
- "Submit and monitor the job script."
keypoints:
- "Parallel programming allows applications to take advantage of
  parallel hardware."
- "The queuing system facilitates executing parallel tasks."
---

Often we need to run a script across many input files or samples, or we need to
run a parameter sweep to determine the best values for a model. Rather than
painstakingly submitting a batch job for every iteration, we can use a SLURM job
array to simplify the task.

If you disconnected, log back in to the cluster.

```
{{ site.local.prompt }} ssh {{ site.remote.user }}@{{ site.remote.login }}
```
{: .language-bash}

## An Illustrative Example

For our example job array, we are going to import an input text file. Each row of the
text file represents a unique sample. We are going to process the data in each row
separately, or in individual job array steps. The output for each array step will
be written to its own output text file.

To begin, use `nano` to create a text file called `input.txt` and enter the table
below (you can copy and paste):

```
{{ site.remote.prompt }} nano input.txt
```
{: .language-bash}

```
SampleID    SampleName    NCats    NDogs
     001         Henry        0        2
     002           Rob        1        1
     003       Harmony        3        0
     004         Nevin        0        0
```

Our task is to add the number of cats **NCats** and the number of dogs **NDogs**
for each sample to determine the total number of pets per sample. For each
sample, we will create an output text file called `sample-<SampleID>.txt`.
The output text file will contain a line of text as follows:

```
<SampleName> has a total of <NCats + NDogs> pets.
```

## 1. Write and Test an SBATCH Script for a Single Sample

Write a batch script for a single iteration of your workflow. We want to
make sure it runs as expected before submitting potentially thousands of
copies of our job to the scheduler. This is often where the most work
needs to be done, therefore this is the longest section of the example
(even though there is no parallelization happening here!).

```
{{ site.remote.prompt }} nano jobtest.sbatch
```
{: .language-bash}

```
#!/bin/bash
#SBATCH --cpus-per-task=1
#SBATCH --mem=500m
#SBATCH --time=00:01

# Specify the input text file
input=$HOME/input.txt

# Select a single row to test
test_row=1

# Extract the SampleID
sample_id=$(awk -v i=$test_row '$1==i {print $1}' $input)

# Extract the SampleName
sample_name=$(awk -v i=$test_row '$1==i {print $2}' $input)

# Extract NCats
ncats=$(awk -v i=$test_row '$1==i {print $3}' $input)

# Extract NDogs
ndogs=$(awk -v i=$test_row '$1==i {print $4}' $input)

# Add ncats and ndogs to get npets
npets=$(expr $ncats + $ndogs)

# Specify output text filename
output=$HOME/sample-${sample_id}.txt

# Write to output file
echo "$sample_name has a total of $npets pets." >> $output
```

Now we can submit our test sbatch script.

```
{{ site.remote.prompt }} sbatch jobtest.sbatch
```
{: .language-bash}

We can check the status of our job with `squeue`.

```
{{ site.remote.prompt }} squeue --me
```
{: .language-bash}

And then when the job is complete, we can verify that we get the expected output.

```
{{ site.remote.prompt }} cat sample-001.txt
```
{: .language-bash}

```
Henry has a total of 2 pets.
```
{: .output}

## 2. Set the `--array` SBATCH Directive

The SBATCH directive `--array` tells the scheduler how many
copies of your code should run, or rather, how many job array steps there should
be. In the case of our example, we have four samples so `--array=1-4`.

1. Create a copy of `jobtest.sbatch` and name it `jobarray.sbatch`.
2. Add `#SBATCH --array=1-4` to our list of SBATCH directives.

```
{{ site.remote.prompt }} cp jobtest.sbatch jobarray.sbatch
{{ site.remote.prompt }} nano jobarray.sbatch
```
{: .language-bash}

```
#!/bin/bash
#SBATCH --cpus-per-task=1
#SBATCH --mem=500m
#SBATCH --time=00:01
#SBATCH --array=1-4

# Specify the input text file
input=$HOME/input.txt

# Select a single row to test
test_row=1
...
```

## 3. Use the `$SLURM_ARRAY_TASK_ID` Variable

Much like the iterator of a `for` loop, the `$SLURM_ARRAY_TASK_ID` variable
is used to handle individual tasks or job array steps. In our example
where `#SBATCH --array=1-4`, we will have four separate array tasks corresponding
to our four samples. For the first array task where we process the first sample,
`$SLURM_ARRAY_TASK_ID` will be set to `1`; in the second array task,
`$SLURM_ARRAY_TASK_ID` will equal `2`, and so on.

In our original test of a single task, we created the variable `test_row` and
set it to `1`. We then used `test_row` to extract variables from a single
row of `input.txt`. Using `test_row` in this way was sort of like setting
`SLURM_ARRAY_TASK_ID=1`.

In our production job, we will remove the line creating the `test_row` variable.
We will then replace all instances of `$test_row` with `$SLURM_ARRAY_TASK_ID`.

```
{{ site.remote.prompt }} nano jobarray.sbatch
```
{: .language-bash}

```
#!/bin/bash
#SBATCH --cpus-per-task=1
#SBATCH --mem=500m
#SBATCH --time=00:01
#SBATCH --array=1-4

# Specify the input text file
input=$HOME/input.txt

# Extract the SampleID
sample_id=$(awk -v i=$SLURM_ARRAY_TASK_ID '$1==i {print $1}' $input)

# Extract the SampleName
sample_name=$(awk -v i=$SLURM_ARRAY_TASK_ID '$1==i {print $2}' $input)

# Extract NCats
ncats=$(awk -v i=$SLURM_ARRAY_TASK_ID '$1==i {print $3}' $input)

# Extract NDogs
ndogs=$(awk -v i=$SLURM_ARRAY_TASK_ID '$1==i {print $4}' $input)

# Add ncats and ndogs to get npets
npets=$(expr $ncats + $ndogs)

# Specify output text filename
output=$HOME/sample-${sample_id}.txt

# Write to output file
echo "$sample_name has a total of $npets pets." >> $output
```

## 4. Submit the Job Array

Now we can submit our job array to the scheduler. We only have to run the `sbatch`
command once, and SLURM will handle the creation of all the individual array tasks.

```
{{ site.remote.prompt }} sbatch jobarray.sbatch
```
{: .language-bash}

```
Submitted batch job 277394
```
{: .output}

When you submit the job array, you will receive a main job ID.

```
{{ site.remote.prompt }} squeue --me
```
{: .language-bash}

```
             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
          277394_1    normal jobarray  SUNetID  R       0:06      1 wheat-01
          277394_2    normal jobarray  SUNetID  R       0:06      1 wheat-01
          277394_3    normal jobarray  SUNetID  R       0:06      1 wheat-01
          277394_4    normal jobarray  SUNetID  R       0:06      1 wheat-01
```
{: .output}

Each individual array task will also receive its own subID.

> ## Advanced Job Array Options
>
> * **`%N`**: By default, SLURM will try to submit all your array tasks at once.
> If you are trying to run thousands of tasks, you will probably run into
> job submission limits. You can use the **`%N`** option to limit the number of
> simultaneous tasks. For example, `#SBATCH --array=1-100%10` will submit 100
> total array tasks, 10 at a time.
>
> * **Select array steps:** You can specify particular array steps by changing the
> value of `--array`.
>     - `--array=5` will only submit array task 5.
>     - `--array=1,6,9` will submit array tasks 1, 6, and 9.
>     - `--array=0-100:10` will submit array tasks 0, 10, 20, 30, 40, 50, 60,
>       70, 80, 90, and 100.
{: .callout}


{% include links.md %}

