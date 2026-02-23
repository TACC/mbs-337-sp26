Homework 04
===========

**Due Date: Tuesday, February 10 by 11:00am CST**

From Sequence to Structure
--------------------------

This homework builds on Unit 3 (FASTA, FASTQ, and mmCIF). You will use the same tools
(Biopython ``SimpleFastaParser``, ``SeqIO``, ``MMCIFParser``) but to solve new problems:
FASTA statistics and length-based filtering, FASTQ quality filtering and output, and
mmCIF multi-chain analysis and coordinate extraction.

**Input files:**

* **FASTA:** A multi-sequence FASTA file named ``immune_proteins.fasta``. Download with:

.. code-block:: console

   wget https://github.com/TACC/mbs-337-sp26/raw/refs/heads/main/docs/unit03/sample-data/immune_proteins.fasta.gz
   gunzip immune_proteins.fasta.gz

* **FASTQ:** A FASTQ file named ``sample1_rawReads.fastq``.

.. code-block:: console

   wget https://github.com/TACC/mbs-337-sp26/raw/refs/heads/main/docs/unit03/sample-data/sample1_rawReads.fastq.gz
   gunzip sample1_rawReads.fastq.gz

* **mmCIF:** The hemoglobin structure **4HHB**. Download with:

.. code-block:: console

   wget https://files.rcsb.org/download/4HHB.cif.gz
   gunzip 4HHB.cif.gz

Exercise 1: Count residues in FASTA file
----------------------------------------

Create a Python script called ``exercise1.py`` that reads ``immune_proteins.fasta``
and prints:

  1. The total number of sequences in the file
  2. The total number of residues in the file
  3. The accession ID and length of the longest sequence in the file
  4. The accession ID and length of the shortest sequence in the file

Example output:

.. code-block:: text

   Num Sequences: 321
   Total Residues: 196434
   Longest Accession: P78527 (4128 residues)
   Shortest Accession: Q9HCY8 (104 residues)

**Requirements:**

* Use ``SimpleFastaParser`` from ``Bio.SeqIO.FastaIO``.
* Correctly extract the accession from each header.
* Match the example output format exactly.

Exercise 2: Write New FASTA File
--------------------------------

Create a Python script called ``exercise2.py`` that reads ``immune_proteins.fasta`` 
using ``SimpleFastaParser`` again. This time, your script should **write out** a new 
FASTA file called ``long_only.fasta`` containing only the sequences longer than or 
equal to 1000 residues. Each output record must be a valid FASTA with the original headers 
format preserved. 

**Requirements:**

* Use ``SimpleFastaParser`` from ``Bio.SeqIO.FastaIO``.
* Output ``long_only.fasta`` must be a valid FASTA and should contain 33 sequences.

Exercise 3: FASTQ quality filter and write
-------------------------------------------

Create a Python script called ``exercise3.py`` that:

1. Reads ``sample1_rawReads.fastq`` using ``SeqIO.parse`` with the correct format.
2. Keeps only reads whose **average Phred quality** is at least 30.
3. Writes those filtered reads out to a new FASTQ file named ``sample1_cleanReads.fastq``.
4. Prints to the terminal the total number of reads in the original file and the number of reads that passed quality control.

Example output:

.. code-block:: text

   Total reads in original file: 500
   Reads passing filter: 483

**Requirements:**

* Use ``Bio.SeqIO`` for reading and writing (see `Bio.SeqIO.write() documentation <https://biopython.org/docs/1.76/api/Bio.SeqIO.html>`_).
* Specify the correct quality encoding when reading.
* Match the example output format exactly.

Exercise 4: mmCIF multi-chain summary (4HHB)
--------------------------------------------

Create a Python script called ``exercise3.py`` that:

1. Parses ``4HHB.cif`` with ``MMCIFParser`` from ``Bio.PDB``.
2. Iterates over the full structure hierarchy (all models, all chains).
3. For **each chain**, prints:
   
   * Chain ID
   * Number of **non-hetero-residues** in that chain
   * Number of atoms in the **non-hetero-residues** in that chain

Example Output:

.. code-block:: text

   Chain A: 141 residues, 1069 atoms
   Chain B: 146 residues, 1123 atoms
   Chain C: 141 residues, 1069 atoms
   Chain D: 146 residues, 1123 atoms

.. note:: 

   You may get multiple lines of the following warning printed to the terminal:

   .. code-block:: console

      PDBConstructionWarning: WARNING: Chain D is discontinuous at line xxx

   This is normal behavior when working with multiple chains and is safe to ignore. 

**Requirements:**

* Use ``4HHB.cif``.
* Use ``MMCIFParser`` from ``Bio.PDB.MMCIFParser`` to read the mmCIF file.
* Remember the object hierarchy in Biopython PDB: structure → models → chains → residues → atoms. Use nested ``for`` loops to walk this hierarchy.
* Only when the residue is non-hetero should you increment your residue counter and loop over its atoms to count them.
* Match the example output format exactly.

What to Turn In
---------------

1. Create a ``homework04`` directory in your Git repository (on your VM).
2. Add all four Python scripts (``exercise1.py`` through ``exercise4.py``) to this directory.
3. Place the generated output files in ``homework04``.
4. Add a ``README.md`` in ``homework04`` that:

   * Describes what each script does
   * Explains where to get the input files
   * Includes a section on AI usage (if applicable — see note below)
5. Commit and push your work to GitHub.

**Generated files:** Running your scripts should produce ``long_only.fasta`` (Exercise 2) and ``sample1_cleanReads.fastq`` (Exercise 3). Include these in a subdirectory within ``homework04`` called ``output_files``.

**Expected directory layout:**

.. code-block:: text

   my-mbs337-repo/
   ├── homework04/
   │   ├── exercise1.py
   │   ├── exercise2.py
   │   ├── exercise3.py
   │   ├── exercise4.py
   │   ├── output_files/
   │   │   ├── long_only.fasta
   │   │   └── sample1_cleanReads.fastq
   │   └── README.md

Note on Using AI
----------------

The use of AI to complete this assignment is not recommended, but it is permitted
with the following restrictions:

The use of LLMs (like ChatGPT, Copilot, etc) or any other AI must be rigorously
cited. Any code blocks or text that are generated by an AI model should be
clearly marked as such with in-code comments describing what was generated, how
it was generated, and why you chose to use AI in that instance. The homework
README must also contain a section that summarizes where AI was used in the assignment.

Additional Resources
--------------------

* `Unit 3: FASTA <../unit03/fasta.html>`_
* `Unit 3: FASTQ <../unit03/fastq.html>`_
* `Unit 3: mmCIF <../unit03/mmCIF.html>`_
* `Biopython SeqIO <https://biopython.org/wiki/SeqIO>`_
* `Biopython PDB <https://biopython.org/wiki/The_Biopython_Structural_Biology_FAQ>`_
* `RCSB PDB <https://www.rcsb.org/>`_ — download mmCIF files (e.g. 4HHB)
* Please find us in the class Slack channel if you have any questions!
