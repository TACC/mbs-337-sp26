Nextflow
========

This module introduces Nextflow, arguably one of the most widely-used workflow managers for
bioinformatics applications. Nextflow is designed to be scalable, perform reproducible data analysis,
work on local hardware or on HPC/cloud. A major benefit of Nextflow is the community-developed
nf-core library of workflows that are easy to download and run immediately.

After going through this module, students should be able to:

* Describe core Nextflow concepts and terminology, and compare those to other workflow managers
* Install and configure Nextflow and nf-core on an HPC cluster
* Write, modify, and run simple Nextflow workflows
* Retrieve workflows from nf-core and run them on an HPC cluster



About Nextflow
--------------

The developers of Nextflow took inspiration from Unix: As we know, Unix is a collection of of many
simple command line tools that, together, can do very powerful thing. Similarly, Nextflow was 
designed around the concept of simple "processes" that, together, could be linked into powerful
"workflows". Each process is a standalone task that is language agnostic, and is associated
with one or more inputs and outputs.

Another major concept in Nextflow is the idea of the "channel". The channel is like a conveyor belt
that shuttles data from one step to the next in the workflow. Channels can work through serial
data flows, or support more complex parallel data flows.

The Nextflow syntax, technically a "workflow language", is based on Java and Groovy. The language 
is fairly easy to read and may look somewhat familiar to Python developers. Although there is still
a high learning curve associated with developing workflows in a new language, the time will be well
worth the effort in the long run.



Community Nextflow Pipelines: nf-core
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

**nf-core** is a community effort to develop, curate, and maintain a set of best-practice analysis
pipelines built with Nextflow, with a strong emphasis on reproducibility, portability, and
standardized practices. If you have a
general idea of the type of analysis you want to run (e.g., RNA-seq, variant calling, ChIP-seq),
it is often best to first search nf-core for an existing, well-tested pipeline rather than starting
from scratch.

nf-core also provides a dedicated command-line interface ( ``nf-core``), which includes tools for
discovering pipelines, checking compatibility, and managing configurations, as we will see below.



Core Terminology
~~~~~~~~~~~~~~~~

* **Process:** A computational step with inputs, outputs, and a script
* **Channel:** A data stream that connects processes and passes data between them
* **Executor:** The backend that runs the jobs (i.e. local, SLURM, cloud, etc.)
* **Profile:** A named configuration bundle for running Nextflow



Installation
------------

After logging in to Lonestar6, create a new directory to organize this work. Update the version
of Java, and download and run the Nextflow installer script:

.. code-block:: console

    # organize the work
    [ls6]$ cd $WORK
    [ls6]$ mkdir nextflow-example && cd nextflow-example

    # update java
    [ls6]$ curl -s https://get.sdkman.io | bash
    [ls6]$ source $HOME/.sdkman/bin/sdkman-init.sh
    [ls6]$ sdk install java 17.0.10-tem

    # install nextflow
    [ls6]$ curl -s https://get.nextflow.io | bash
    [ls6]$ chmod +x nextflow

    # move nextflow into an executable path
    [ls6]$ mkdir -p $HOME/.local/bin/
    [ls6]$ mv nextflow $HOME/.local/bin/
    [ls6]$ export PATH=$HOME/.local/bin/:$PATH

    # verify the installation
    [ls6]$ nextflow info


.. note::

    It would be a good idea to add the following two lines to your ~/.bashrc in order to 
    keep Nextflow and the right version of Java in your PATH:

    .. code-block:: text

        source $HOME/.sdkman/bin/sdkman-init.sh
        export PATH=$HOME/.local/bin/:$PATH


The installation process for nf-core is a little bit simpler. It is a Python package that can
be installed with pip, so create a virtual environment and install with the following commands:

.. code-block:: console

    # create a virtual environment
    [ls6]$ python -m venv .venv
    [ls6]$ source .venv/bin/activate

    # install and verify snakemake
    (venv)[ls6]$ pip3 install nf-core
    (venv)[ls6]$ nf-core --version
    (venv)[ls6]$ nf-core pipelines list



Nextflow Usage
--------------

First Process
~~~~~~~~~~~~~

Create a new Nextflow file (called ``hello_world.nf``). The first step is to write a process and 
a workflow that executes that process. Consider the following code:

.. code-block:: java
    :linenos:

    process sayHello {
        output:
        path "hello_world.txt"

        script:
        """
        echo "Hello World!" > hello_world.txt
        """
    }

    workflow {
        main:
        sayHello()
    }

There is one process that takes no inputs and writes one output - a text file called ``hello_world.txt``.
The process is orchestrated within the workflow block, which in this case contains only one item to
execute.

After saving the file, run Nextflow with the following command:

.. code-block:: console

    [ls6]$ nextflow run hello_world.nf
     N E X T F L O W   ~  version 25.10.4

    Launching `hello_world.nf` [suspicious_volhard] DSL2 - revision: 61b9a538a0

    executor >  local (1)
    [14/f3d424] sayHello [100%] 1 of 1 ✔

The console indicates success, so list the files in the current directory to confirm the expected
output is there.

.. tip::

    Having trouble finding the output file? Try ``tree -a .`` and examine all of the output files
    and folders you find. 



Publish Outputs
~~~~~~~~~~~~~~~

After running the first Nextflow workflow once, try running it again with the exact same command.
In contrast to Snakemake, Nextflow by default does not verify that the output is already there - 
rather it runs the workflow again and stores the output in a new ``work`` subdirectory. This is
the default intended behavior of Nextflow. The ``work`` folder is meant to be pupulated with
temporary or intermediate files. We can specify the output file as something we would like 
to keep by adding the following instruction:


.. code-block:: java
    :linenos:
    :emphasize-lines: 15,16,19-24

    process sayHello {
        output:
        path "hello_world.txt"

        script:
        """
        echo "Hello World!" > hello_world.txt
        """
    }

    workflow {
        main:
        sayHello()

        publish:
        first_output = sayHello.out
    }

    output {
        first_output {
            path "."
            mode "copy"
        }
    }

The publish statement instructs Nextflow to copy the output from the sayHello process (``sayHello.out``)
from the temporary folder to a user-defined path, in this case ``"."``. Although, the output block
specifies an output path as ``"."``, that path will be relative to a new, local ``results`` folder
that Nextflow will create. Run Nextflow again and verify that the output file is now in the
expected location:

.. code-block:: console

    [ls6]$ nextflow run hello_world.nf


Next, try running Nextflow with the ``-resume`` flag (Note: only one hyphen) and it will use cached
values if possible:

.. code-block:: console

    [ls6]$ nextflow run hello_world.nf -resume

This is excellent for long workflows that failed part way through - it will use cached values where
possible and generate new outputs only if they are missing.



Variables
~~~~~~~~~

After creating a very simple workflow, the next step is to abstract away from hardcoding values
and filenames where possible. Instead, replace them with variables. Consider the following changes:

.. code-block:: java
    :linenos:
    :emphasize-lines: 2-3,10,16

    process sayHello {
        input:
        val name

        output:
        path "hello_${name}.txt"

        script:
        """
        echo "Hello ${name}" > hello_${name}.txt
        """
    }

    workflow {
        main:
        sayHello(params.input)

        publish:
        first_output = sayHello.out
    }

    output {
        first_output {
            path "."
            mode "copy"
        }
    }

We added a new input to the ``sayHello`` process called ``name``. We pass a value into that input
from within the workflow through a flag called ``params.input`` that in turn is passed on the
command line:

.. code-block:: console

    [ls6]$ nextflow run hello_world.nf --input Joe


.. tip::

    In order to to hard code a default, add a line like the following at the top of the
    Nextflow script:

    .. code-block:: text

        params.input = "Joe"


Channels
~~~~~~~~

Channels are a core component of Nextflow and are a powerful way to scale up input data. The values
passed through a channel can be hardcoded in the workflow, can be dynamically generated, or can
be read in from a file. Consider the following update to the workflow:

.. code-block:: java
    :linenos:
    :emphasize-lines: 16-17

    process sayHello {
        input:
        val name

        output:
        path "hello_${name}.txt"

        script:
        """
        echo "Hello ${name}" > hello_${name}.txt
        """
    }

    workflow {
        main:
        name_ch = channel.of("Alice", "Bob", "Carol")
        sayHello(name_ch)

        publish:
        first_output = sayHello.out
    }

    output {
        first_output {
            path "."
            mode "copy"
        }
    }

Again, run the workflow and verify that the expected output files are generated. Notice that the
process is executed three times, once for each value in the channel.



Multi-Step Workflows
~~~~~~~~~~~~~~~~~~~~

To demonstrate the real potential of Nextflow, next implement a new process to form a two-step
workflow. The second process will take the output from the first process, modify it, and write a
new output. Consider the following code:

.. code-block:: java
    :linenos:
    :emphasize-lines: 14-25,31,35,43-46

    process sayHello {
        input:
        val name

        output:
        path "hello_${name}.txt"

        script:
        """
        echo "Hello ${name}" > hello_${name}.txt
        """
    }

    process sayGoodbye {
        input:
        path input_file

        output:
        path "goodbye_${input_file}"

        script:
        """
        sed s/Hello/Goodbye/ ${input_file} > goodbye_${input_file}
        """
    }

    workflow {
        main:
        name_ch = channel.of("Alice", "Bob", "Carol")
        sayHello(name_ch)
        sayGoodbye(sayHello.out)

        publish:
        first_output = sayHello.out
        second_output = sayGoodbye.out
    }

    output {
        first_output {
            path "."
            mode "copy"
        }
        second_output {
            path "."
            mode "copy"
        }
    }

In this workflow, there is a new process called sayGoodbye which replaces the word Hello with Goodbye
in the specified file, then renames the file with a "``goodbye``" prefix. The output from the ``sayHello``
process is passed directly into the ``sayGoodbye`` process. New blocks are also added to specify
that the output from the second process should also be published to the results folder.



EXERCISE
~~~~~~~~

Modify the script block in the ``sayGoodbye`` process to use the parameter instead of hardcoding the
name in the output file. For example, the output file could be named ``goodbye_${name}.txt`` instead
of ``goodbye_${input_file}``. You will need to also modify how the process is called in the workflow
to pass it a new parameter.



Summary of Important Nextflow Commands
--------------------------------------

A list of some of the more commonly used Nextflow flags and commands include:

* **nextflow run:** run a Nextflow workflow
* **nextflow run -resume:** use cached values if possible
* **nextflow log:** print log of previous runs
* **nextflow clean:** clean up files from previous runs
* **nextflow help:** print help message



Hands-On Exercise: Variant Calling
----------------------------------

Recall the workflow we implemented in `Unit 11 <../unit11/hands_on_lab.html>`_ for variant calling. 
It was a four-step workflow that took in a reference genome and a set of aligned SAM files as input,
and output a VCF file as the final result.

Collect the following two inputs again:

.. code-block:: console

    [ls6]$ cp /work/03439/wallen/public/samtools_example/ecoli_reads_aligned.sam ./
    [ls6]$ cp /work/03439/wallen/public/samtools_example/ecoli_NC_008253.fna ./

A summary of the commands that were required to run the original workflow (manually) are as follows:

.. code-block:: text

    module load biocontainers
    module load samtools/ctr-1.20--h50ea8bc_0
    module load bcftools/ctr-1.21--h3a4d415_1
    
    samtools view -b -S -o ecoli_reads_aligned.bam ecoli_reads_aligned.sam
    samtools sort -o ecoli_reads_aligned_sorted.bam ecoli_reads_aligned.bam
    samtools index ecoli_reads_aligned_sorted.bam    
    bcftools mpileup -f ecoli_NC_008253.fna -o ecoli_variants.vcf ecoli_reads_aligned_sorted.bam

Modify the following template Nextflow workflow file to implement the same workflow as above 
using four processes, one for each step. Publish the final output only, the variant call file.

.. code-block:: java
    :linenos:

    params.reads_sam = "${projectDir}/ecoli_reads_aligned.sam"                                                                                                                                                          
    params.reference = "${projectDir}/ecoli_NC_008253.fna"                                                    
    
    process convertToBam {                               
        input:                                                                                                
        path input_sam                                   
                                                         
        output:                                          
        path "${input_sam}.bam"                                                                               
                                                         
        script:                                          
        """                                              
        samtools view -b -S -o ${input_sam}.bam ${input_sam}                                                  
        """                                              
    }                                                    
                                                         
    process sortBam {                                    
        ...
    }
    
    process indexBam {
        ...
    }
    
    process variantCalling {
        ...
    }
    
    workflow {                                           
        main:                                            
        convertToBam(params.reads_sam)                                                                        
        sortBam(...)                        
        indexBam(...)                            
        variantCalling(...)                                                                                                                                                     
    
        publish:                                         
        vcf_out = ...                                                                          
    }                                                    
    
    output                                               
    {                                                    
        vcf_out {                                        
            path "."                                     
            mode "copy"                                  
        }                                                
    }                                                    

.. hint::

    To specify which containers you need for each step, refer to the Nextflow documentation on
    `containers <https://docs.seqera.io/nextflow/container>`_. You will use a config file similar to:

    .. code-block:: text

        process {
            withName:convertToBam {
                container = '/work/projects/singularity/TACC/bio_modules/biocontainers/samtools/samtools-1.20--h50ea8bc_0.sif'
            }
            withName:sortBam {
                container = '...'
            }
            withName:indexBam {
                container = '...'
            }
            withName:variantCalling {
                container = '...'
            }
        }
        apptainer {
            enabled = true
        }

Run the workflow with ``nextflow run <workflow file>`` and verify the output.


Hands-On Exercise: nf-core
--------------------------

As mentioned previously, nf-core is a community effort to develop, curate, and maintain a set of 
best-practice analysis pipelines built with Nextflow. If you have a general idea of the type of 
analysis you want to run (e.g., RNA-seq, variant calling, ChIP-seq), it is often best to first 
search nf-core for an existing, well-tested pipeline rather than starting from scratch.

You can search the nf-core pipelines with the following command:

.. code-block:: console

    (venv)[ls6]$ nf-core pipelines list

And you can run a demo FastQC pipeline with the following command:

.. code-block:: console

    (venv)[ls6]$ nextflow run nf-core/demo -profile test,apptainer --outdir results

The ``-profile`` flag specifies to use some pre-canned test data, and to use Apptainer to automatically
pull the necessary containers. The ``--outdir`` flag specifies where to put the output. After running, 
check the results folder to verify that the expected output is there.



Other Considerations and Next Steps
-----------------------------------

Nextflow has many other features not mentioned here. A few of the most important features to be 
aware of include:

* **Config File** Nextflow supports a powerful configuration system that allows you to specify parameters, resource requirements, and other settings in a separate config file. This can help keep your workflow code clean and organized, and makes it easier to manage different configurations for different runs.
* **Profiles:** Profiles are a powerful way to manage different configurations for running the same workflow in different environments. For example, you could have one profile for running on your local machine, and another profile for running on an HPC cluster. Profiles can specify different executors, different resource requirements, and different parameters.
* **Executors:** Nextflow supports a wide range of executors, including local execution, SLURM, SGE, LSF, Kubernetes, and more. This allows you to run the same workflow on different computing environments without changing the workflow code.
* **Error Handling:** Nextflow has built-in error handling and retry mechanisms. If a process fails, Nextflow can automatically retry it a specified number of times. You can also specify custom error handling logic in your workflow.

Debugging workflows in an interactive session on a compute node can be very helpful for 
understanding how the workflow is executing and for troubleshooting issues. When ready to run a 
workflow in batch mode, simply put the Nextflow command in a SLURM script and submit it to the 
scheduler. Take care to source the appropriate environment and load any necessary modules in the SLURM script before running:

.. code-block:: console

    #!/bin/bash
    #SBATCH -J nextflow_job
    #SBATCH -o nextflow_job.o%j
    #SBATCH -e nextflow_job.e%j
    #SBATCH -p development
    #SBATCH -N 1
    #SBATCH -n 1
    #SBATCH -t 00:30:00
    #SBATCH -A OTH24028

    # assuming you are in working directory with your workflow file
    source $HOME/.sdkman/bin/sdkman-init.sh
    export PATH=$HOME/.local/bin/:$PATH

    nextflow run hello_world.nf




Additional Resources
--------------------

* `Nextflow Docs <https://docs.seqera.io/nextflow>`_
* `Nextflow Container Docs <https://docs.seqera.io/nextflow/container>`_
* `nf-core Docs <https://nf-co.re/docs/>`_
* `nf-core Pipeline Catalog <https://nf-co.re/pipelines>`_
* `nf-core Example Pipeline <https://nf-co.re/docs/get_started/run-your-first-pipeline>`_