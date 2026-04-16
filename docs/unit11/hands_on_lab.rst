Hands-on Lab: Variant Calling
=============================

A typical variant calling workflow consists of the following steps:


* Step 1: Extract DNA from sample (experimental)
* Step 2: Sequence DNA (experimental)
* Step 3: Align reads to reference genome (computational)
* Step 4: Identify genomic variants (computational)


For the purposes of this lab, we will focus on Step 4 and assume the first three steps have
already been performed. After Step 3, our data consists of a file of aligned reads in ``sam``
format, and a reference genome in ``fna`` format. We will be using the samtools software 
package to do Step 4.


Identify Genomic Variants
-------------------------

First, find an appropriate ``samtools`` package in the module system and load it into your environment.
Record the commands for this step for your ``job.slurm`` script. 

Then, copy the following inputs to your working directory:

.. code-block:: console

    [ls6]$ cp /work/03439/wallen/public/samtools_example/ecoli_reads_aligned.sam ./
    [ls6]$ cp /work/03439/wallen/public/samtools_example/ecoli_NC_008253.fna ./

And assemble to following steps into your ``job.slurm`` script:

**Step 4a: First convert aligned reads from sam format to bam format**

.. code-block:: console

   samtools view -b -S -o ecoli_reads_aligned.bam ecoli_reads_aligned.sam

**Step 4b: Sort the bam file**

.. code-block:: console

   samtools sort -o ecoli_reads_aligned_sorted.bam ecoli_reads_aligned.bam

**Step 4c: Index the sorted bam file (sorting and indexing enables fast, efficient access)**

.. code-block:: console

   samtools index ecoli_reads_aligned_sorted.bam

**Step 4d: Identify genomic variants**

.. code-block:: console

   samtools mpileup -g -f ecoli_NC_008253.fna ecoli_reads_aligned_sorted.bam > ecoli_variants.bcf

**Step 4e: Use bcftools (packaged with samtools) to look for SNPs**

.. code-block:: console

   bcftools call -c -v ecoli_variants.bcf > ecoli_variants.vcf


The job should take less than 10 minutes on one node. Submit the job, monitor the job status in
the queue, and look for the results.
Make sure you understand which file was generated at each step and what it represents. Make sure
you can identify whether this job finished successfully or if ther were any errors.

BONUS
~~~~~

Visualize the genome in an idev session by performing the following:

.. code-block:: console

   samtools tview ecoli_reads_aligned_sorted.bam ecoli_NC_008253.fna


Additional Resources
--------------------

* Materials adapted from `SAMtools: A Primer <https://github.com/ecerami/samtools_primer>`_

