Snakemake
=========

This module introduces Snakemake, a highly popular Python-based workflow manager used in bioinformatics
and scientific computing. Snakemake can have a high learning curve, but it is a powerful and flexible
tool for defining and executing complex workflows with many dependencies.

After going through this module, students should be able to:

* Describe the core concepts of Snakemake and how it differs from GNU Make
* Install Snakemake in a Python virtual environment
* Write and execute a simple Snakemake workflow with multiple rules and dependencies
* Use wildcards to create flexible rules that can be applied to many files
* Use Snakemake's command-line interface to run workflows with different options and flags
* Implement a real-world workflow (docking) in Snakemake and execute it at scale



About Snakemake
---------------

Snakemake borrows concepts and syntax from GNU Make, specifically the idea of defining "rules" that
specify how to create output files from input files using shell commands. However, Snakemake extends
this concept with a more powerful Python-based syntax and a more sophisticated dependency management
system. 

A simple overview of Snakemake rules includes:

* Can contain zero or more input files, and/or zero or more output files
* Can contain a shell command that specifies how to create the output files from the input files
* By default, the first rule is typically a special rule called "all" that specifies as input(s) the
  desired final output(s) of the workflow
* Execution order of rules is determined by the dependencies between input and output files, forming
  a directed acyclic graph (DAG) of tasks

Advanced features of Snakemake rules include:

* Wildcards can be used to create flexible rules that can be applied to many files
* Rules can be parameterized specifically with designated containers, core counts, logfiles, and 
  other resources
* Snakemake is designed to be idempotent, meaning that if you run it multiple times, it will only
  execute the steps that are necessary to create new desired outputs without recreating existing
  outputs from a previous run
* Creates a hidden ``.snakemake`` directory to track runs and manage intermediate files and provenance

With this more complicated rule syntax comes a steeper learning curve than GNU Make, but it also
allows for much more powerful and flexible workflows. We must change our way of thinking about 
analyses to fully take advantage of Snakemake. One of the core concepts is that *Snakemake works 
through the rules backwards*. You the user specify what final output file you want to create, and
Snakemake will work backwards through the DAG of rules to figure out what combination of steps and
inputs can lead to that final output. Then, it will execute those steps in the correct order, and
only those steps that are necessary.


Core Terminology
~~~~~~~~~~~~~~~~

* **Snakefile:** The main file where rules and workflow logic are defined
* **Rule:** A unit of work that specifies inputs, outputs, and the command(s) to run
* **Wildcards:** Placeholders that allow rules to generalize across many files (e.g., multiple samples)
* **DAG (Directed Acyclic Graph):** The dependency graph of all jobs Snakemake will run, derived from your rules and target outputs


Installation
------------

After logging in to Lonestar6, create a new directory to organize this work, then create a Python
virtual environment inside that directory and install Snakemake.

.. code-block:: console

    # organize the work
    [ls6]$ cd $WORK
    [ls6]$ mkdir snakemake-example && cd snakemake-example

    # confirm you have a recent Python module loaded
    [ls6]$ module list
    Currently Loaded Modules:                                          
      1) intel/19.1.1   3) autotools/1.4   5) cmake/4.1.1   7) xalt/3.1
      2) impi/19.0.9    4) python3/3.9.7   6) pmix/3.2.3    8) TACC    

    # create a virtual environment
    [ls6]$ python -m venv .venv
    [ls6]$ source .venv/bin/activate

    # install and verify snakemake
    (venv)[ls6]$ pip3 install snakemake
    (venv)[ls6]$ snakemake --help


.. warning::

    Take careful note of which version of Snakemake installed. Is it the latest version? Why or
    why not? Refer to the Python Package Index (`PyPI <https://pypi.org/project/snakemake/>`_) to
    investigate.

    When forcing an older version of a package, you may need to downgrade other libraries as well.
    If you see an error about the library ``pulp``, downgrade it by pip installing an older version.


In an environment with an older default Python (such as Lonestar6), you may prefer to install
Snakemake via Conda, or you may prefer to use something like ``uv`` to manage Python versions
*and* virtual environments.



Snakemake Usage
---------------

First Rule
~~~~~~~~~~

Let's take a first look at a very simple Snakemake workflow. Create a new file called ``Snakefile``
in your current directory and add the following content:

.. code-block:: python

    rule all:
        input: "hello_world.txt"

    rule hello_world:
        output: "hello_world.txt"
        shell: "echo 'Hello World!' > hello_world.txt"

As mentioned, the first rule in the Snakefile is typically a special rule called ``all``. The *input*
for this rule is the desired final *output* of the workflow. In this case, we want to create file 
called ``hello_world.txt``.

Snakemake looks through the rules and identifies another rule (``hello_world``) that can create the
desired output file. It then looks at the inputs for that rule (there are none), and executes the
necessary shell command to create the output file.

Run Snakemake with the following command:

.. code-block:: console

    (venv)[ls6]$ snakemake --cores 1
    # or
    (venv)[ls6]$ snakemake -c1

Note that by default, Snakemake is looking for a file called ``Snakefile`` in the current directory.
The only required flag is ``--cores`` (or ``-c``), which specifies how many CPU cores Snakemake can
use to run jobs in parallel.


Dependencies
~~~~~~~~~~~~

As the complexity of the workflow increases, we can add more rules with more inputs and outputs.
Each rule should correspond to a specific step (or command) in the workflow. Snakemake will work
backwards from the desired final output to figure out the order in which to run the steps.

Add the following new lines to your Snakefile, below the existing rules:

.. code-block:: python
    :emphasize-lines: 4-7

    rule all:
        input: "hello_universe.txt"

    rule mod_file:
        input: "hello_world.txt"
        output: "hello_universe.txt"
        shell: "sed -i 's/World/Universe/g' hello_world.txt && cp hello_world.txt hello_universe.txt"

    rule hello_world:
        output: "hello_world.txt"
        shell: "echo 'Hello World!' > hello_world.txt"


Execute the workflow again with the same command as before. What happens if you execute the command
twice in a row?


Variables
~~~~~~~~~

The ``input`` and ``output`` fields of Snakemake rules are typically assigned to variables which
to be used in the shell command. This allows for a bit more flexibility, readability, and avoids
hardcoding file names in the shell command. For example, try rewriting the previous Snakefile as:

.. code-block:: python
    :emphasize-lines: 7,11

    rule all:
        input: "hello_universe.txt"

    rule mod_file:
        input: "hello_world.txt"
        output: "hello_universe.txt"
        shell: "sed -i 's/World/Universe/g' {input} && cp {input} {output}"

    rule hello_world:
        output: "hello_world.txt"
        shell: "echo 'Hello World!' > {output}"


Wildcards
~~~~~~~~~

The main way to add flexibility to Snakefiles is to use **wildcards**. Wildcards use bracket notation
to map a list of, e.g., input names to a list of output names. This allows you to write a single rule that can be applied to many
files without having to write out a separate rule for each file. The ``expand()`` function is used
to dynamically generate the list of input files for the ``all`` rule based on the list of names provided.

.. code-block:: python

    names = ["Alice", "Bob", "Carol"]

    rule all:
        input: expand("hello_{name}.txt", name=names)

    rule hello_name:
        output: "hello_{name}.txt"
        shell: "echo 'Hello {wildcards.name}!' > hello_{wildcards.name}.txt"

Note that the ``{name}`` in the output of the ``hello_name`` rule corresponds to the ``{name}`` in
the input of the ``all`` rule. This allows Snakemake to figure out which files to create and how to
create them based on the list of names provided. In the ``shell`` command, the ``{wildcards.name}``
syntax is used to access the value of the wildcard for that specific rule execution.

Snakemake can be passed desired output filenames directly, too. In this way it overrides the default
behavior of looking for the ``all`` rule.

.. code-block:: console

    (venv)[ls6]$ snakemake -c1 hello_Joe.txt

EXERCISE
~~~~~~~~

Add a ``sleep 5`` statement to the shell command in the ``hello_name`` rule, then run Snakemake
again. What happens? How long does it take to run? What happens if you change the number of cores
with the ``-c`` flag?


Summary of Important Snakemake Flags
------------------------------------

Some of the most commonly used Snakemake flags we will see include:

* ``-c1``: Run the workflow with 1 core (no parallelization)
* ``-c4``: Run the workflow with 4 cores (parallelization)
* ``--dry-run``: Perform a dry run to see what would be executed without actually running the commands
* ``--forceall``: Force the execution of all rules and all their dependencies, even if the output files already exist
* ``--dag``: Print the DAG of the workflow
* ``--dag | dot -Tpdf > dag.pdf``: Save the DAG as a PDF
* ``--summary``: Print a summary of the workflow
* ``--unlock``: Unlock the workflow if it is locked due to a previous failed run
* ``--rerun-incomplete``: Rerun any incomplete jobs from a previous run
* ``--clean``: Clean up intermediate files
* ``--printshellcmds``: Print the shell commands that are being executed - great for debugging
* ``--help``: Print the help message with all available flags and options

.. note::

    Many of the above flags can be used in combination.


Hands-On Exercise: Docking
--------------------------

Let us revisit the docking workflow from `Unit 11 <../unit11/batch_job_submission.html>`_
and re-implement it in Snakemake. First, double check that you still have Snakemake in your PATH and
copy over the materials again into this new directory:

.. code-block:: console

   (venv)[ls6]$ cp -r /work/03439/wallen/public/autodock_vina_example_2/* ./

Recall we have an input configuration file, an input receptor file, and 944 ligand files. From
Unit 11, we determined that the command to dock an individual ligand would resemble:

.. code-block:: console

    (venv)[ls6]$ vina --config config.in --receptor 2FOM.pdbqt --ligand ligands/ligand.pdbqt --out output/ligand_out.pdbqt


And a command to dock a batch of ligands would resemble:

.. code-block:: console

    (venv)[ls6]$ vina --config config.in --receptor 2FOM.pdbqt --batch ligands/*pdbqt --dir output/


.. warning::

    Which command should be co-opted into our Snakemake workflow?


Begin a new Snakemake file and write out the workflow for a single case (one ligand). Then we 
will generalize the workflow to apply it to all cases (all ligands). The Snakefile should have an
"all" rule which specifies a final output, and a "run_docking" rule which executes the appropriate
vina line as a shell command. For example:

.. code-block:: python

    rule all:
        input: "output/ZINC04632727_out.pdbqt"

    rule run_docking:
        input: "<PUT INPUT HERE>"
        output: "<PUT OUTPUT HERE>"
        shell: "vina <VINA OPTIONS HERE>"


Once that is written for a single ligand case, test it by running Snakemake. Use
the ``--dry-run`` and ``--printshellcmds`` flags to help debug in the beginning.

.. code-block:: console

    (venv)[ls6]$ snakemake --dry-run --printshellcmds
    # ...
    (venv)[ls6]$ snakemake -c1


.. warning:: 

    There is a high likelihood that the above Snakemake execution failed unless you remembered to
    do something important ahead of time. Refer back to the
    `Vina example <../unit11/batch_job_submission.html>`_ to check.

If the Snakefile successfully runs for a single ligand, then generalize the workflow to apply to all
ligands. This entails using wildcards to create a single rule that can be applied to all ligands files.
You will need to do a little bit of Python coding to figure out how to itemize all ligand file 
names. For example:

.. code-block:: python
    
    import glob

    ligand_files = glob.glob("ligands/*.pdbqt")
    ligand_names = [f.split("/")[1].split(".")[0] for f in ligand_files]

    rule all:
        input: expand("output/{ligand}_out.pdbqt", ligand=ligand_names)

    rule run_docking:
        input: "<PUT INPUT HERE>"
        output: "<PUT OUTPUT HERE>"
        shell: "vina <VINA OPTIONS HERE>"


Once you have filled out the Snakefile, test it again with appropriate debugging flags. Then:

1. Run the workflow with 1 core for a little while and monitor how long it takes
2. After a few minutes, kill the execution with Ctrl+C
3. Run the workflow with 4 cores and monitor how long it takes
4. After a few minutes, kill the execution with Ctrl+C
5. Run Snakemake with the ``--dry-run`` or ``--summary`` flags to see what progress has been made
6. Use as many cores as you need to complete the docking quickly
7. Once complete, use Snakemake commands againt to verify that all expected output files are present

Note: Our configuration file is set to only use 1 CPU core per ligand. Snakemake will distribute
different tasks (ligands) to different cores. Each task only using 1 core (as specified in the 
configuration file). Here, we can envision two docking schemes:

* **Scheme 1:** Set CPU in config.in to 1 and run Snakemake with 120 tasks (-c120)
* **Scheme 2:** Set CPU in config.in to 4 and run Snakemake with 30 tasks (-c30)
* **Scheme 3:** Set CPU in config.in to 10 and run Snakemake with 12 tasks (-c12)

How would you go about determining which scheme is best for this workflow?
What are the tradeoffs between these schemes?




Other Considerations and Next Steps
-----------------------------------

Some final considerations for keeping your Snakemake workspace organized:

* Keep your Snakefile under version control to track changes
* Clearly document what inputs are required (and where they come from) for the start of your workflow(s)
* Consider breaking large workflows into multiple smaller workflows that can be executed independently
* Consider using configuration files (e.g., YAML) to manage parameters and file paths in a more organized way
* Consider using Snakemake's built-in support for containers to manage software dependencies and ensure reproducibility across platforms

When running interactively on a compute node, you can call the Snakemake CLI directly. If 
running at scale in batch mode, simply put the ``snakemake`` command into your SLURM script. Don't forget
to source your virtual environment so ``snakemake`` is in your PATH when the job runs, e.g.:

.. code-block:: console

    #!/bin/bash
    #SBATCH -J snakemake_job
    #SBATCH -o snakemake_job.o%j
    #SBATCH -e snakemake_job.e%j
    #SBATCH -p development
    #SBATCH -N 1
    #SBATCH -n 1
    #SBATCH -t 00:30:00
    #SBATCH -A OTH24028

    # assuming you are in working directory with your Snakefile and virtual environment
    module use /work/03439/wallen/public/modulefiles
    module load autodock_vina/1.2.3
    source .venv/bin/activate
    snakemake -c4




Additional Resources
--------------------

* `Snakemake Docs <https://snakemake.readthedocs.io/en/stable/>`__
* `UV Docs <https://docs.astral.sh/uv/>`_
