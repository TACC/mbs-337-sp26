Introduction to Workflow Managers
==================================

**Workflows** are a formal, structured way to express an analysis. They are typically a series of ordered
steps with shared dependencies, parameters, and references. The same workflow can be executed repeatedly
on different datasets, ensuring consistent and reproducible processing.

**Workflow managers** not only orchestrate these steps, but also hande file and metadata tracking,
provenance, and failure recovery so that interrupted runs can be resumed without starting from 
scratch. 

This module introduces the core concepts and terminology of workflow managers, and explains Why
they are essential for modern, reproducible computational research. After going through this module,
students should be able to:

* Define workflows and workflow managers
* Identify scenarios where workflow managers should be applied
* Describe the benefits of workflow managers over *ad hoc* scripting



Why You Should Use Workflow Managers
------------------------------------

Suppose you are a researcher in a computational biology group. You may want to start using a 
workflow manager if any of the following are true:

* You have a multi-step analysis that is difficult to run manually, or has many interdependent steps
* You have a large number of samples that need to be processed in the same way
* You want to ensure that your analysis is reproducible and portable across different computing environments
* You want to automate your analysis to save time and reduce the risk of human error

Workflow managers help in all of the above scenarios. Importantly, when you write a workflow for
what you are doing, you create a formal expression of your analysis that is portable and reproducible.
This is a key step not only in computational biology, but also in the scientific process.


.. note::

    HPC clusters, like Lonestar6, are generally optimized for *high-performance computing*, or 
    large and highly coupled tasks with a lot of inter-task communication.

    HPC clusters also support *HTC*, or *high-throughput computing*, which typically refers to many,
    many independent tasks.

    The workflow managers discussed in these sections are excellent at fitting HTC-type jobs into 
    an HPC environment.




Features of Workflow Managers
-----------------------------

Many workflow managers are available (we will see a few below). They almost all share some common 
features:

* Can generally run anywhere - from your local laptop to a large HPC clusters
* Scale up to fit the resource you are running on
* Wrap around your normal software (including containers) without any changes
* Replace manual steps with automation and reduce the chance of human error
* Track provenance and metadata to ensure reproducibility
* Handle failures and allow you to resume from where you left off without starting from scratch


Push vs Pull
~~~~~~~~~~~~

One of the key decisions when choosing a workflow manager is whether to use a push or pull model for
executing tasks.

* **Push** - Generally refers to user-initiated execution. You send work to a resource to run when
  you have some data and are ready to do work. This is ideal for HPC clusters like those at TACC
  because you only use nodes when work is ready to be performed. Although this is the most efficient
  way to use resources, you may end up waiting for a while in the queue before your jobs start.

* **Pull** - In a pull model, the workflow manager first gathers compute resources to do work, then
  waits idly for data to be ready for analysis. The major benefit here is that the work can start
  immediately once input data is available, however it can be an inneficient use of resources if 
  there are large gaps between when resources are allocated and when data is ready to be processed.
  (Not too dissimilar from an idev session).

The workflow managers we will discuss in this unit are *push* style, which are better for a shared
HPC cluster environment. *Pull* style workflow managers are better suited for dedicated resources,
like your class Jetstream VM.


GUI vs CLI
~~~~~~~~~~

Another key decision is whether to use a workflow manager with a command line interfance (CLI) or
a graphical user interface (GUI).

* **CLI** - Command line interfaces are generally more flexible and powerful, but have a steeper learning curve.
  They are ideal for users who are comfortable with the command line and want to have fine-grained control
  over their workflows.

* **GUI** - Graphical user interfaces are generally more user-friendly and easier to learn, but may 
  have limitations in terms of flexibility and control. They are ideal for users who prefer a visual
  interface and may not be as comfortable with the command line.



Common Workflow Managers in Bioinformatics
------------------------------------------

Each tool has its own strengths and weaknesses. As each tool increases in capabilities, it also
increases in complexity. The best tool for you will depend on your specific needs and preferences.


GNU Make
~~~~~~~~

`GNU <https://www.gnu.org/software/make/manual/make.html>`_ 
make is a classic automation tool, typically used for building software. It has been used to 
manage simple workflows for decades. It is installed by default on most Unix-like systems. 

Pros:

* Simple and easy to use
* Good for small to medium-sized workflows
* Widely used and supported

Cons:

* Limited scalability
* Not ideal for complex workflows with many dependencies


Snakemake
~~~~~~~~~

`Snakemake <https://snakemake.readthedocs.io/en/stable/>`_
is a Python-based workflow manager that evolved from GNU Make. It provides a more powerful
and flexible way to define and execute workflows, while maintaining a syntax that is relatively
similar to Make. Snakemake is widely used in bioinformatics and has a large and active community.

Pros:

* Python-based, which makes it easy to learn and use
* Scales well to large workflows with many dependencies
* Supports a wide range of execution environments, including HPC clusters and cloud platforms
* Provides built-in support for containerization

Cons:

* Steeper learning curve than GNU Make
* Requires writing Snakefiles, which can be complex for very large workflows


Nextflow
~~~~~~~~~

`Nextflow <https://docs.seqera.io/nextflow>`_
is a Groovy-based workflow manager that is designed for scalability and flexibility. It is
widely used in bioinformatics with a huge library of community-developed pipelines. Nextflow is
particularly well-suited for complex workflows with many dependencies, and it provides built-in
support for containerization and cloud execution.

Pros:

* Groovy-based, which is a powerful and flexible language
* Scales well to large workflows with many dependencies
* Supports a wide range of execution environments, including HPC clusters and cloud platforms
* Provides built-in support for containerization
* Wealth of community-contributed pipelines and modules via nf-core

Cons:

* Steeper learning curve than GNU Make
* Requires writing Nextflow scripts, which can be complex for very large workflows
* Default pull-based execution model may not be ideal for shared HPC clusters


Galaxy
~~~~~~

`Galaxy <https://galaxyproject.org/get-started/>`_ 
is a web-based platform for data analysis that includes a workflow manager. It is designed
to be user-friendly and accessible to users with little or no programming experience. Galaxy provides
a graphical interface for defining and executing workflows, and it supports a wide range of
bioinformatics tools and resources. Although the interface is web-based, the backend can be configured
to run at scale on HPC clusters.

Pros:

* User-friendly web interface
* No programming experience required
* Supports a wide range of bioinformatics tools and resources
* Can be configured to run on HPC clusters

Cons:

* May not be as flexible or powerful as CLI-based workflow managers
* May require more resources to run the web interface
* Not ideal for very large workflows with many dependencies



Additional Considerations
-------------------------

It is safe to sy that workflow managers will take a lot of time and energy up front to learn the
syntax and translate your analyses into formal workflows. However, the benefits of using a workflow
manager far outweight the initial investment. In the long run, workflow managers will always save 
you time, provide protection against human error, ensure reproducibility, and track provenance.
They are an essential tool for modern computational research.



Additional Resources
--------------------

* `GNU Make Docs <https://www.gnu.org/software/make/manual/make.html>`__
* `Snakemake Docs <https://snakemake.readthedocs.io/en/stable/>`__
* `Nextflow Docs <https://docs.seqera.io/nextflow>`__
* `Galaxy Docs <https://galaxyproject.org/get-started/>`_
