Working with Sequence Data (FASTA)
====================================

In this hands-on module, we will learn how to work with the FASTA data format.
This is one of the most fundamental file formats in bioinformatics, used extensively
for storing biological sequencing data (DNA, RNA, and protein sequences).

After going through this module, students should be able to:

* Identify and understand valid FASTA files
* Read FASTA files into Python data structures
* Parse sequence headers and extract metadata
* Write sequences to FASTA files

FASTA Format Basics
-------------------

FASTA (pronounced "fast-A") is a text-based format used to represent nucleotide or
protein sequences. It was developed in the 1980s and remains one of the most widely
used formats in bioinformatics today. FASTA files are simple, human-readable, and
can be easily processed with basic text manipulation tools.

A FASTA file consists of one or more sequence records. Each record contains:

1. **A header line** (also called a definition line) that starts with a ``>`` (greater-than) symbol
2. **One or more sequence lines** containing the actual sequence data

Here is an example of a simple FASTA file with three protein sequences downloaded from `Uniprot <https://www.uniprot.org/>`_:

.. code-block:: text

   >sp|P02144|MYG_HUMAN Myoglobin OS=Homo sapiens OX=9606 GN=MB PE=1 SV=2
    MGLSDGEWQLVLNVWGKVEADIPGHGQEVLIRLFKGHPETLEKFDKFKHLKSEDEMKASE
    DLKKHGATVLTALGGILKKKGHHEAEIKPLAQSHATKHKIPVKYLEFISECIIQVLQSKH
    PGDFGADAQGAMNKALELFRKDMASNYKELGFQG

   >sp|P68871|HBB_HUMAN Hemoglobin subunit beta OS=Homo sapiens OX=9606 GN=HBB PE=1 SV=2
    MVHLTPEEKSAVTALWGKVNVDEVGGEALGRLLVVYPWTQRFFESFGDLSTPDAVMGNPK
    VKAHGKKVLGAFSDGLAHLDNLKGTFATLSELHCDKLHVDPENFRLLGNVLVCVLAHHFG
    KEFTPPVQAAYQKVVAGVANALAHKYH

    >sp|P01308|INS_HUMAN Insulin OS=Homo sapiens OX=9606 GN=INS PE=1 SV=1
    MALWMRLLPLLALLALWGPDPAAAFVNQHLCGSHLVEALYLVCGERGFFYTPKTRREAED
    LQVGQVELGGGPGAGSLQPLALEGSLQKRGIVEQCCTSICSLYQLENYCN


In this example:

* Each sequence starts with a ``>`` symbol followed by header information
* The header contains a UniProt-specific identifier and additional descriptive information (see Uniprot's `documentation <https://www.uniprot.org/help/fasta-headers>`_)
* The sequence lines contain amino acid single-letter codes
* Sequences can span multiple lines (as shown above)
* Blank lines between records are optional but often used for readability

Sequences are expected to follow the standard `IUB/IUPAC <https://www.bioinformatics.org/sms/iupac.html>`_ 
amino acid and nucleic acid codes, with some important exceptions:

* Lower-case letters are allowed
* A single hyphen or dash can be used to represent a gap in the sequence
* In amino acid sequences, ``*`` is sometimes used to represent a stop codon

There are multiple filename extensions associated with FASTA files:

.. list-table::
    :align: center
    :header-rows: 1

    * - Extension
      - Meaning
      - Notes 
    * - .fasta, .fas, .fa
      - generic FASTA
      - Any generic FASTA file
    * - .fna 
      - FASTA nucleic acid
      - Used generically to specify nucleic acids
    * - .ffn
      - FASTA nucleotide of gene regions
      - Contains coding regions for a genome
    * - .faa 
      - FASTA amino acid
      - Contains amino acid sequences
    * - .mpfa
      - FASTA amino acids
      - Contains multiple protein sequences
    * - .frn 
      - FASTA non-coding RNA
      - Contains non-coding RNA regions for a genome, e.g. tRNA, rRNA


Working with FASTA files
------------------------

Let's get some practice working with FASTA files. We'll use VSCode for these exercises. 
Open a VSCode RemoteSSH session (``Cmd+Shift+P`` -> ``RemoteSSH``) and create a new terminal
(``Cmd+Shift+P`` -> ``Terminal: Create New Terminal``).

Within the terminal inside VSCode on your class VM, navigate to your ``mbs-337`` project directory
and create a new directory called ``working-with-bio-data``. 

Then, let's create our input file:

* Copy and paste the contents of `this file <https://raw.githubusercontent.com/TACC/mbs-337-sp26/refs/heads/main/docs/unit03/sample-data/sequences.fasta>`_ into a new file called ``sequences.fasta`` 

Helpful Linux Commands
~~~~~~~~~~~~~~~~~~~~~~

There are a few linux commands that we can do from the command line to get some high-level information
about our FASTA file. 

First, let's take a look at the FASTA file:

.. code-block:: bash

    # Print the first 8 lines
    [mbs-337]$ head -n 8 sequences.fasta         

    # Print the lines starting with '>'
    [mbs-337]$ grep ">" sequences.fasta

    # Count the number of lines starting with '>'
    [mbs-337]$ grep -c ">" sequences.fasta 


Read FASTA from File
~~~~~~~~~~~~~~~~~~~~~

Good news! The ``Biopython`` library that we installed earlier in the course makes working with
sequence data very straightforward. Let's activate our Python3 virtual environment, and create a new
file called ``fasta_ex.py``.

For reading FASTA files, we will use **SimpleFastaParser** from Biopython.  ``SimpleFastaParser`` 
is a lightweight, memory-efficient generator that yields ``(header, sequence)`` tuples. 
This makes it great for working with large FASTA files.

.. admonition:: Tuple vs List

    Tuples and lists are both ordered items or values. The key differences between them are:

        * Tuples are **unchangeable**, and are contained in parentheses ``()``
        * Lists are **changeable**, and are contained in square brackets ``[]``

First, import ``SimpleFastaParser`` from ``Bio.SeqIO.FastaIO`` and read in our FASTA file:

.. code-block:: python3

    from Bio.SeqIO.FastaIO import SimpleFastaParser

    with open('sequences.fasta', 'r') as f:
        for values in SimpleFastaParser(f):
            print(values)
            break # stop after the first 

.. code-block:: text

    ('sp|P36222|CH3L1_HUMAN Chitinase-3-like protein 1 OS=Homo sapiens OX=9606 GN=CHI3L1 PE=1 SV=2', 'MGVKASQTGFVVLVLLQCCSAYKLVCYYTSWSQYREGDGSCFPDALDRFLCTHIIYSFAN...')

For each sequence record, a tuple of strings is returned: the FASTA header line (without the leading '>'
character), and the sequence itself. 

We can access the data within the tuples by iterating over both the header and the sequence:

.. code-block:: python3

    from Bio.SeqIO.FastaIO import SimpleFastaParser

    with open('sequences.fasta', 'r') as f:
        for header, sequence in SimpleFastaParser(f):
            print(f"Header: {header}")
            print(f"Sequence: {sequence}")
            break

.. code-block:: console

    Header: sp|P36222|CH3L1_HUMAN Chitinase-3-like protein 1 OS=Homo sapiens OX=9606 GN=CHI3L1 PE=1 SV=2
    Sequence: MGVKASQTGFVVLVLLQCCSAYKLVCYYTSWSQYREGDGSCFPDALDRFLCTHIIYSFANISNDHIDTWEWNDVTLYGMLNTLKNRNPNLKTLLSVGGWNFGSQRFSKIASNTQSRRTFIKSVPPFLRTHGFDGLDLAWLYPGRRDKQHFTTLIKEMKAEFIKEAQPGKKQLLLSAALSAGKVTIDSSYDIAKISQHLDFISIMTYDFHGAWRGTTGHHSPLFRGQEDASPDRFSNTDYAVGYMLRLGAPASKLVMGIPTFGRSFTLASSETGVGAPISGPGIPGRFTKEAGTLAYYEICDFLRGATVHRILGQQVPYATKGNQWVGYDDQESVKSKVQYLKDRQLAGAMVWALDLDDFQGSFCGQDLRFPLTNAIKDALAAT...

Formatting the data in a tuple format like this allows us to easily perform actions on our sequence data.
For example, let's say I wanted to make my sequence headers easier to read. I could use the ``.split()``
method to break the header string into pieces:

.. code-block:: python3

    from Bio.SeqIO.FastaIO import SimpleFastaParser

    with open('sequences.fasta', 'r') as f:
        for header, sequence in SimpleFastaParser(f):
            parts = header.split()
            print(parts)
            break

.. code-block:: console

    ['sp|P36222|CH3L1_HUMAN', 'Chitinase-3-like', 'protein', '1', 'OS=Homo', 'sapiens', 'OX=9606', 'GN=CHI3L1', 'PE=1', 'SV=2']

Our header looks quite different now! Using the default settings of ``.split()``, we now have a **list** of each
substring contained in our header string, each separated where there was a whitespace in the original string. 

.. tip::

    You can always learn more about a function or method using Python3's interactive mode. For example, 
    we could get additional information about the syntax and options associated with the ``.split()``
    string method by doing the following:

    .. code-block:: python3

        >>> help(str.split)

    .. code-block:: text 
        :emphasize-lines: 3, 6, 12

        Help on method_descriptor:

        split(self, /, sep=None, maxsplit=-1) unbound builtins.str method
            Return a list of the substrings in the string, using sep as the separator string.

            sep
                The separator used to split the string.

                When set to None (the default value), will split on any whitespace
                character (including \n \r \t \f and spaces) and will discard
                empty strings from the result.
            maxsplit
                Maximum number of splits.
                -1 (the default value) means no limit.

            Splitting starts at the front of the string and works to the end.

    Breaking down the first highlighted line:

    1. ``self`` means the string you're calling ``.split()`` on. Essentially, it means:

        .. code-block:: python3

            "hello world".split()
            # here, self == "hello world"

    2. The ``/`` means: Everything before this must be passed to ``.split()`` positionally, not within the parentheses. 
    3. ``sep`` is the separator. If you provide it, the string will be split on that separator. If you don't provide a separator, the default behavior (None) is to split on any whitespace. 
    4. ``maxsplit`` controls how many splits are allowed. The default (-1) means no limit. 

So, now that we know how ``.split()`` works on strings, let's see what happens when we separate on the ``|`` character:

.. code-block:: python3

    from Bio.SeqIO.FastaIO import SimpleFastaParser

    with open('sequences.fasta', 'r') as f:
        for header, sequence in SimpleFastaParser(f):
            parts = header.split('|')
            print(parts)
            break

.. code-block:: console

    ['sp', 'P36222', 'CH3L1_HUMAN Chitinase-3-like protein 1 OS=Homo sapiens OX=9606 GN=CHI3L1 PE=1 SV=2']

From here, I could format my data so that the sequence information only contains the accession ID by 
grabbing the second item within this list. I could also perform an action on the sequences to determine
the length of each sequence:

.. code-block:: python3

    from Bio.SeqIO.FastaIO import SimpleFastaParser

    with open('sequences.fasta', 'r') as f:
        for header, sequence in SimpleFastaParser(f):
            parts = header.split('|')
            print(f"Accession: {parts[1]}, Length: {len(sequence)}")

We can use ``SimpleFastaParser`` to build a list of dictionaries with the same structure:

.. code-block:: python3

    from Bio.SeqIO.FastaIO import SimpleFastaParser

    sequences = []

    with open('sequences.fasta', 'r') as f:
        for header, sequence in SimpleFastaParser(f):
            parts = header.split('|')
            entry = {
                "id": parts[1],
                "description": header,
                "sequence": sequence,
                "length": len(sequence)
            }
            sequences.append(entry)

    print(sequences[0])

Now we can do things like:

* **Count how many proteins are longer than 500 amino acids**

.. code-block:: python3

    count = 0

    for seq in sequences:
        if seq['length'] > 500:
            count += 1  # this is shorthand for "count = count + 1"
    
    print(f"Number longer than 500 aa: {count}")

* **Find the longest sequence**

.. code-block:: python3

    longest_length = 0
    longest_seq = None  # placeholder string

    for seq in sequences:
        if seq['length'] > longest_length:
            longest_length = seq['length']
            longest_seq = seq

    print(longest_seq["description"])


Write FASTA to File
-------------------

Let's say we are only interested in continuing our analysis on a particular set of sequences
within this FASTA file. We have a CSV file that contains a list of accessions, and we want to
create a new FASTA file containing **only the matching sequences (and their headers)**.

For this activity, you'll need to copy/paste the contents of `this file <https://raw.githubusercontent.com/kbeavers/mbs-337-sp26/refs/heads/main/docs/unit03/sample-data/accessions.csv>`__
into a new file called ``accessions.csv``. Notice that this CSV has a header row called 'accession'.

Read in the CSV
~~~~~~~~~~~~~~~~

First, we'll use the ``csv`` module to read in the accessions as a list:

.. code-block:: python3

    import csv
    from Bio.SeqIO.FastaIO import SimpleFastaParser

    # Read accessions from CSV into a list
    accessions_to_keep = []
    with open('accessions.csv', 'r') as f:
        reader = csv.DictReader(f)
        for row in reader:
            accessions_to_keep.append(row['accession'])

In the above code, ``csv.DictReader`` treats the first row of ``f`` as the header.
For every following row, it creates a dictionary where the key == 'accession'
and the value is the accession itself:

.. code-block:: python3

    {'accession': 'P36222'}
    {'accession': 'Q13258'}
    etc.

We then iterate over this ``reader``, where each row is now a **dictionary**, and
append the value associated with each 'accession' key to an empty list called ``accessions_to_keep``. 
After the loop finishes, the list looks like:

.. code-block:: python3

    ['P36222', 'Q13258', 'Q6W5P4', 'Q8TAX7']

Filter the sequences
~~~~~~~~~~~~~~~~~~~~~

Now, we read in ``sequences.fasta`` with ``SimpleFastaParser`` and also create a new
output file called ``filtered.fasta`` where we will write our output to. 

For each sequence in ``sequences.fasta``, we extract the accession. If it's in our
``accessions_to_keep`` list, we write the header and sequence to our new file, 
making sure that we properly format our output to match FASTA requirements:

.. code-block:: python3
    :emphasize-lines: 11,12

    # Read input FASTA and output matches to a new output fasta
    with open('sequences.fasta', 'r') as infile, open('filtered.fasta', 'w') as outfile:
        for header, sequence in SimpleFastaParser(infile):

            # Grab the accession ID 
            parts = header.split('|')
            accession = parts[1]

            # Filter by accession ID, write output to new fasta file
            if accession in accessions_to_keep:
                outfile.write(f">{header}\n")
                outfile.write(f"{sequence}\n")

Notice the special formatting that we have to do when we write our output file:

* Our header line must start with ``>``
* We add a newline character (``\n``) to make sure that a new line is created before we continue the loop

When you run this script, ``filtered.fasta`` will contain only the
sequences whose primary accession appears in ``accessions.csv``. You can confirm
by checking the number of headers and comparing to the number of accessions in your CSV.


Additional Resources
--------------------

* `FASTA Format Specification <https://en.wikipedia.org/wiki/FASTA_format>`_
* `NCBI FASTA Format Guide <https://www.ncbi.nlm.nih.gov/genbank/FASTAformat/>`_
* `Biopython Library <https://biopython.org/>`_ - 
* `SeqIO Module Documentation <https://biopython.org/wiki/SeqIO>`_ 
