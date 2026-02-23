Python Refresher
================

To be successful in this class, students should be able to:

* Write and execute Python code on the class server
* Use variables, lists, and dictionaries in Python
* Write conditionals using a variety of comparison operators
* Write useful while and for loops
* Arrange code into clean, well organized functions
* Read input from and write output to a file
* Import and use standard and non-standard Python libraries

Topics covered in this module include:

* Data types and variables (ints, floats, bools, strings, type(), print())
* Arithmetic operations (+, -, \*, /, \*\*, %, //)
* Lists and dictionaries (creating, interpreting, appending)
* Conditionals and control loops (comparison operators, if/elif/else, while, for, break, continue, pass)
* Functions (defining, passing arguments, returning values)
* File handling (open, with, read(), readline(), strip(), write())
* Importing libraries (import, random, names, pip)


Log in to the Class Server via VSCode
-------------------------------------

All computing for this course will take place on Linux virtual machines (VMs). 
For this lesson and following lessons, we'll be using the VSCode IDE to connect to your student VMs. 

.. note:: 

   **If you've already set up VSCode Remote-SSH you're all set!** 
   VSCode will handle the connection to your VM automatically. You can use VSCode's 
   integrated terminal to run the interactive Python interpreter and execute Python scripts. 
   All the examples in this guide can be run directly from VSCode.

   If you haven't set up VSCode yet, please follow the VSCode setup instructions before proceeding.

If you can't access the class server yet, a local or web-based Python 3
environment will work for this guide. However, you will need to access the class
server for future lectures.

Try this `Python 3 environment in a browser <https://www.brython.info/tests/console.html?lang=en>`_.


Data Types and Variables
------------------------

Start up the interactive Python interpreter in VSCode's integrated terminal:

.. code-block:: console

   [mbs-337]$ python3
   Python 3.x.x (main, Nov  6 2025, 13:44:16) [GCC 13.3.0] on linux
   Type "help", "copyright", "credits" or "license" for more information.
   >>>

.. tip::

   To exit the interpreter, type ``quit()``.

The most common data types in Python are similar to other programming languages.
For this class, we probably only need to worry about **integers**, **floats**,
**booleans**, and **strings**.

Assign some values to variables by doing the following:

.. code-block:: python3

   >>> gene_count = 42
   >>> protein_mass = 55.5
   >>> is_expressed = True      # or False, notice capital letters
   >>> gene_name = 'BRCA1'

In Python, you don't have to declare type. Python figures out the type
automatically. Check using the ``type()`` function:

.. code-block:: python3

   >>> type(gene_count)
   <class 'int'>
   >>> type(protein_mass)
   <class 'float'>
   >>> type(is_expressed)
   <class 'bool'>
   >>> type(gene_name)
   <class 'str'>

Print the values of each variable using the ``print()`` function:

.. code-block:: python3

   >>> print(gene_count)
   42
   >>> print('gene_count')
   gene_count

(Try printing the others as well). And, notice what happens when we print with
and without single quotes? What is the difference between ``gene_count`` and
``'gene_count'``?

You can convert between types using a few different functions. For example, when
you read in data from a file, numbers are often read as strings. Thus, you may
want to convert the string to integer or float as appropriate:

.. code-block:: python3

   >>> str(gene_count)      # convert int to string
   >>> str(protein_mass)    # convert float to string
   >>> type(gene_count)
   >>> type(protein_mass)

What do you notice about the above ``type()`` commands? Is the output what you expected?

Using ``str()`` prints a string of the original variable to the console, but it does not actually change the original variable itself —
``gene_count`` and ``protein_mass`` are still whatever you assigned earlier (an integer and a float, respectively).

If you want ``gene_count`` and ``protein_mass`` to become strings, you must reassign the variable:

.. code-block:: python3

   >>> gene_count = str(gene_count)
   >>> protein_mass = str(protein_mass)
   >>> type(gene_count)
   >>> type(protein_mass)

Alternatively, you can assign new variables as a different type of the original variable:

.. code-block:: python3

   >>> gene_count_int = int(gene_count)
   >>> type(gene_count)
   >>> type(gene_count_int)
   >>>
   >>> protein_mass_float = float(protein_mass)
   >>> type(protein_mass)
   >>> type(protein_mass_float)

Arithmetic Operations
---------------------

Next, we will look at some basic arithmetic. You may be familiar with the
standard operations from other languages:

.. code-block:: text
   :emphasize-lines: 1

   Operator   Function          Example   Result
   +          Addition          1+1       2
   -          Subtraction       9-5       4
   *          Multiplication    2*2       4
   /          Division          8/4       2
   **         Exponentiation    3**2      9
   %          Modulus           5%2       1
   //         Floor division    5//2      2


Try a few things to see how they work:

.. code-block:: python3

   >>> print(2+2)
   >>> print(355/113)
   >>> print(10%9)
   >>> print(3+5*2)
   >>> print('ATGC' + 'CGTA')
   >>> print('gene' + str(1))
   >>> print('A' * 5)

Also, carefully consider how arithmetic options may affect type:

.. code-block:: python3

   >>> concentration1 = 5/2
   # What output do we expect from the following commands?

   >>> type(concentration1)
   >>> print(concentration1)

   >>> concentration2 = 6/2
   >>> type(concentration2)
   >>> print(concentration2)

.. admonition:: Check Your Understanding

   Which operator can we use if we want ``concentration2`` to be an integer instead of a float?

Lists and Dictionaries
----------------------

**Lists** are a data structure in Python that can contain multiple elements.
They are ordered, they can contain duplicate values, and they can be modified.
Declare a list with square brackets as follows:

.. code-block:: python3

   >>> gene_list = ['BRCA1', 'TP53', 'EGFR', 'MYC']
   >>> type(gene_list)
   <class 'list'>
   >>> print(gene_list)
   ['BRCA1', 'TP53', 'EGFR', 'MYC']

Access individual list elements:

.. code-block:: python3

   >>> print(gene_list[0])
   BRCA1
   >>> type(gene_list[0])
   <class 'str'>
   >>> print(gene_list[2])
   EGFR

Create an empty list and add things to it:

.. code-block:: python3

   >>> expression_levels = []
   >>> expression_levels.append(5.2)     # 'append()' is a method of the list class
   >>> expression_levels.append(3.8)
   >>> expression_levels.append(12.1)
   >>> expression_levels.append(2**3)
   >>> print(expression_levels)
   [5.2, 3.8, 12.1, 8]
   >>> type(expression_levels)
   <class 'list'>
   >>> type(expression_levels[1])
   <class 'float'>

Lists are not restricted to containing one data type. Combine the lists together
to demonstrate:

.. code-block:: python3

   >>> mixed_data = gene_list + expression_levels
   >>> print(mixed_data)
   ['BRCA1', 'TP53', 'EGFR', 'MYC', 5.2, 3.8, 12.1, 8]

Another way to access the contents of lists is by slicing. Slicing supports a
start index, stop index, and step taking the form: ``mylist[start:stop:step]``.
Only the first colon is required. If you omit the start, stop, or :step, it is
assumed you mean the beginning, end, and a step of 1, respectively. Here are
some examples of slicing:

.. code-block:: python3

   >>> dna_sequence = ['A', 'T', 'G', 'C', 'A', 'A', 'A']
   >>> print(dna_sequence[0:2])     # returns the first two bases
   ['A', 'T']
   >>> print(dna_sequence[:2])      # if you omit the start index, it assumes the beginning
   ['A', 'T']
   >>> print(dna_sequence[-2:])     # returns the last two bases (omit the stop index and it assumes the end)
   ['A', 'A']
   >>> print(dna_sequence[:])       # returns the entire sequence
   ['A', 'T', 'G', 'C', 'A', 'A', 'A']
   >>> print(dna_sequence[::2])     # return every other base (step = 2)
   ['A', 'G', 'A', 'A']

.. note::

   If you slice from a list, it returns an object of type list. If you access a
   list element by its index, it returns an object of whatever type that element
   is. The choice of whether to slice from a list, or iterate over a list by
   index, will depend on what you want to do with the data.


**Dictionaries** are another data structure in Python that contain key:value
pairs. They maintain the order in which items were inserted (as of Python 3.7), they cannot contain duplicate keys, and they
can be modified. Create a new dictionary using curly brackets:

.. code-block:: python3

   >>> gene_info = {
   ...   'name': 'BRCA1',
   ...   'chromosome': 17,
   ...   'function': 'DNA repair',
   ...   'disease': 'breast cancer'
   ... }
   >>> type(gene_info)
   <class 'dict'>
   >>> print(gene_info)
   {'name': 'BRCA1', 'chromosome': 17, 'function': 'DNA repair', 'disease': 'breast cancer'}
   >>> print(gene_info['name'])
   BRCA1

As your data changes over time, so too can values stored in dictionaries.
Add new key:value pairs to the dictionary as follows:

.. code-block:: python3

   >>> gene_info['protein_length'] = 1863
   >>> print(gene_info['protein_length'])
   1863


Many other methods exist to access, manipulate, interpolate, copy, etc., lists
and dictionaries. We will learn more about them as we encounter them later
in this course.

Conditionals and Control Loops
------------------------------

Python **comparison operators** allow you to add conditions into your code in
the form of ``if`` / ``elif`` / ``else`` statements. Valid comparison operators
include:

.. code-block:: text
   :emphasize-lines: 1

   Operator   Comparison                 Example   Result
   ==         Equal                      1==2       False
   !=         Not equal                  1!=2       True
   >          Greater than               1>2        False
   <          Less than                  1<2        True
   >=         Greater than or equal to   1>=2       False
   <=         Less Than or equal to      1<=2       True

A valid conditional statement might look like:

.. code-block:: python3

   >>> expression1 = 10.5
   >>> expression2 = 20.3
   >>>
   >>> if (expression1 > expression2):           # notice the colon
   ...     print('expression1 is higher')        # notice the indent
   ... elif (expression2 > expression1):
   ...     print('expression2 is higher')
   ... else:
   ...     print('expression levels are equal')


In addition, conditional statements can be combined with **logical operators**.
Valid logical operators include:

.. code-block:: text
   :emphasize-lines: 1

   Operator   Description                           Example
   and        Returns True if both are True         a < b and c < d
   or         Returns True if at least one is True  a < b or c < d
   not        Negate the result                     not( a < b )

For example, consider the following code:

.. code-block:: python3

   >>> expression1 = 10.5
   >>> expression2 = 20.3
   >>>
   >>> if (expression1 < 100 and expression2 < 100):
   ...     print('both expression levels are below threshold')
   ... else:
   ...     print('at least one expression level exceeds threshold')

The ``not`` operator negates a boolean value. For example:

.. code-block:: python3

   >>> expression1 = 10.5
   >>> expression2 = 20.3
   >>>
   >>> if not (expression1 > expression2):
   ...     print('expression1 is not higher than expression2')
   >>> else:
   ...   print('expression1 is higher than expression2')

.. admonition:: Check Your Understanding

   What output do you expect from the following command?

   .. code-block:: python3

      >>> expression1 = 10.0
      >>> expression2 = 20.0
      >>> threshold = 15.0
      >>>
      >>> if expression1 < threshold and expression2 > threshold:
      ...     if expression2 > expression1 * 2:
      ...         print('High gene activation')
      ...     else:
      ...         print('Moderate gene activation')
      >>> elif expression1 >= threshold or expression2 < threshold:
      ...     print('Threshold check failed')
      >>> else:
      ...     print('Error: No match')

----

**While loops** also execute according to conditionals.
They will continue to execute as long as a condition is True. 

For example:

.. code-block:: python3

   >>> base_count = 0                     
   >>>
   >>> while (base_count < 10):           
   ...     print(base_count)              
   ...     base_count = base_count + 1    

Let's trace through what happens step-by-step:

* **Initial state**: ``base_count = 0``
* **Iteration 1**: Check ``0 < 10`` → True, so print ``0``, then set ``base_count = 1``
* **Iteration 2**: Check ``1 < 10`` → True, so print ``1``, then set ``base_count = 2``
* **Iteration 3**: Check ``2 < 10`` → True, so print ``2``, then set ``base_count = 3``
* ... (continues for iterations 4-9) ...
* **Iteration 10**: Check ``10 < 10`` → False, so exit the loop

.. tip::

   **Important**: Notice that we increment ``base_count`` inside the loop. If we forgot to do this, 
   ``base_count`` would always be 0, the condition ``0 < 10`` would always be True, and the loop 
   would run forever (an infinite loop)! Always make sure your while loop has a way to eventually 
   make the condition False.

   If you get stuck, you can terminate a process in Linux with ``control`` + ``C``.

The ``break`` statement can also be used to escape loops:

.. code-block:: python3

   >>> base_count = 0
   >>>
   >>> while (base_count < 10):
   ...     print(base_count)
   ...     base_count = base_count + 1
   ...     if (base_count==10):
   ...         break
   ...     else:
   ...         continue

----

**For loops** in Python are useful when you need to execute the same set of
instructions over and over again. They are especially great for iterating over
lists:

.. code-block:: python3

   >>> gene_list = ['BRCA1', 'TP53', 'EGFR', 'MYC']
   >>>
   >>> for gene in gene_list:
   ...     print(gene)
   >>>
   >>> for gene in gene_list:
   ...     if (gene == 'TP53'):
   ...         pass                    # do nothing
   ...     else:
   ...         print(gene)

You can also use the ``range()`` function to iterate over a range of numbers.
This function can take one, two, or three arguments:

* ``range(stop)`` -- starts at 0, goes up to (but not including) stop, increments by 1
* ``range(start, stop)`` -- starts at start, goes up to (but not including) stop, increments by 1
* ``range(start, stop, step)`` -- starts at start, goes up to (but not including) stop, increments by step

Here are some examples:

.. code-block:: python3

   >>> for i in range(10):
   ...     print(i)
   # Prints: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9
   >>>
   >>> for position in range(10, 100, 5):
   ...     print(position)
   # Prints: 10, 15, 20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70, 75, 80, 85, 90, 95

**Nested loops** are loops inside other loops. The inner loop completes all its iterations for each
iteration of the outer loop. This is useful when you need to iterate over multiple dimensions of data.

For example, if you have three replicates of three samples under three conditions, you can print all combinations of
replicates, samples, and conditions like so:

.. code-block:: python3

   >>> for replicate in range(1,4):
   ...     for sample in range(1,4):
   ...         for condition in range(1,4):
   ...             print( f'Replicate {replicate}, Sample {sample}, Condition {condition}' )

In the code above, the ``f''`` prefix before a string creates an **f-string** (formatted string literal),
which allows you to embed Python expressions directly inside the string. Any expression inside curly braces 
``{}`` will be evaluated and inserted into the string. 

Functions
---------

**Functions** are blocks of codes that are run only when we call them. We can
pass data into functions, and have functions return data to us. Functions are
absolutely essential to keeping our code clean and organized.

.. note::

   The code is getting a little bit more complicated now. It will be better to
   stop running in the interpreter's interactive mode, and start writing our
   code in Python scripts.

Creating and Running Python Scripts in VSCode
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

From this point forward, we'll be writing Python code in files (scripts) rather than using the interactive interpreter.
We'll use VSCode to create and edit these files.

If you haven't already, make sure you're connected to your VM using VSCode Remote-SSH
(see the VSCode setup instructions earlier in this unit).

.. note::
   Any extensions you downloaded on your local VSCode (including the Python extension)
   will also need to be downloaded and installed on the virtual machine. Make sure to install the **Python**,
   **Pylance**, and **Ruff** extensions that we installed on your local machine. 

1. Open the integrated terminal in VSCode
2. Create a directory called ``mbs-337`` in ``/home/ubuntu`` and navigate into it:

   .. code-block:: console

      [mbs-337]$ pwd
      /home/ubuntu
      [mbs-337]$ mkdir mbs-337
      [mbs-337]$ cd mbs-337

3. Create a new Python file in VSCode:

   - Click **File → New File** (or press ``Ctrl+N`` / ``Cmd+N``)
   - Save the file as ``function_test.py``
   - Make sure you're saving in the current directory (``/home/ubuntu/mbs-337``) 


Enter the following text into the script:

.. code-block:: python3
   :linenos:

   def print_gene_name():
       print('BRCA1')

   print_gene_name()

To execute the script, run it from the terminal (you should already be in the ``mbs-337`` directory):

.. code-block:: console

   [mbs-337]$ python3 function_test.py
   BRCA1

.. note::

   Future examples from this point on will assume familiarity with using VSCode
   to create and edit Python files, and executing scripts using the integrated terminal.
   We will just be showing the contents of the script and console output.

More advanced functions can take parameters and return results. 

.. code-block:: python3
   :linenos:

   def add5(value):
      return(value + 5)

   final_number = add5(10)
   print(final_number)

.. code-block:: console

   15

We can also pass multiple parameters to a function:

.. code-block:: python3
   :linenos:

   def calculate_fold_change(control, treatment):
       return (treatment / control)

   fold_change = calculate_fold_change(10.5, 21.0)
   print(f'Fold change: {fold_change}')

.. code-block:: console

   Fold change: 2.0

It is a good idea to put your list operations into a function in case you plan
to iterate over multiple lists:

.. code-block:: python3
   :linenos:

   def find_genes_starting_with(mylist, prefix):
       for gene in mylist:
           if (gene[0] == prefix):  # a string (gene) can be interpreted as a list of chars!
               print(gene)

   gene_list = ['BRCA1', 'BRCA2', 'TP53', 'EGFR']
   find_genes_starting_with(gene_list, 'B')

.. code-block:: console

   BRCA1
   BRCA2

There are many more ways to call functions, including handling an arbitrary
number of arguments, passing keyword / unordered arguments, assigning default
values to arguments, and more.

File Handling
-------------

The ``open()`` function does all of the file handling in Python. It takes two
arguments - the *filename* and the *mode*. The possible modes are read (``r``),
write (``w``), append (``a``), or create (``x``).

For example, to read a file do the following:

.. code-block:: python3
   :linenos:

   with open('/usr/share/dict/words', 'r') as f:
       for i in range(5):
           print(f.readline())               

.. code-block:: text

   A

   AA

   AAA

   AA's

   AB

.. tip::

   ``with open() as f:`` – This is a **context manager**. It ensures the file is automatically closed
   when you're done, even if an error occurs. The code inside the ``with`` block has access to the file
   through the variable ``f``. Opening files using the ``with`` statement is generally recommended as 
   best practice for file handling. 

You may have noticed in the output above that there are blank lines between each word. 
Every line in a text file ends with a hidden newline character (``\n``) so that when 
you view the file, each word appears on its own line. ``f.readline()`` will return
the line *including* its trailing newline character:

.. code-block:: python3

   f.readline()  # returns "A\n"

``print()`` adds its own newline character by default. The result you end up with is
two newline characters per line:

.. code-block:: text

   "A\n" + "\n"

Stripping the newline character from the original string
is the easiest way to solve this problem:

.. code-block:: python3
   :linenos:

   with open('/usr/share/dict/words', 'r') as f:
       for i in range(5):
           print(f.readline().strip('\n'))

.. code-block:: text

   A
   AA
   AAA
   AA's
   AB

If we wanted to read the whole file and store it as a list, we can use the ``.read()`` method to read the entire file
as one string (``"A\nAA\nAAA\n..."``) followed by the ``.splitlines()`` method that will split the string into a list
of lines and automatically remove the newline characters from each line:

.. code-block:: python3
   :linenos:

   word_names = []

   with open('/usr/share/dict/words', 'r') as f:
       word_names = f.read().splitlines()

   for i in range(5):
       print(word_names[i])

.. code-block:: text

   A
   AA
   AAA
   AA's
   AB


Write output to a new file on the file system; make sure you are attempting to
write somewhere where you have permissions to write:

.. code-block:: python3
   :linenos:

   gene_list = ['BRCA1', 'TP53', 'EGFR', 'MYC']

   with open('gene_list.txt', 'w') as f:
       for gene in gene_list:
           f.write(gene)

.. code-block:: console

   (in gene_list.txt)
   BRCA1TP53EGFRMYC

Hmm... the output file is lacking in newlines this time.
Try adding newline characters to your output:

.. code-block:: python3
   :linenos:

   gene_list = ['BRCA1', 'TP53', 'EGFR', 'MYC']

   with open('gene_list.txt', 'w') as f:
       for gene in gene_list:
           f.write( f'{gene}\n' )

.. code-block:: console

   (in gene_list.txt)
   BRCA1
   TP53
   EGFR
   MYC


Now notice that the original line in the output file is gone - it has been
overwritten. Be careful if you are using write (``w``) vs. append (``a``).

Importing Libraries
-------------------

The Python built-in functions, some of which we have seen above, are useful but
limited. Part of what makes Python so powerful is the huge number and variety
of libraries that can be *imported*. For example, if you want to work with
random numbers, you have to import the 'random' library into your code, which
has a method for generating random numbers called 'random'.

.. code-block:: python3
   :linenos:

   import random  # Load `random` library into your program

   for i in range(5):
       print(random.random())  # From the `random` library, run the `random()` function

.. code-block:: bash

   0.09816538597136149
   0.3602086014874525
   0.5582198241503482
   0.49855010922872045
   0.14930820354681074

More information about using the ``random`` library can be found in the
`Python docs <https://docs.python.org/3/library/random.html>`_

Some libraries that you might want to use are not included in the official
Python distribution - called the *Python Standard Library*. Libraries written
by the user community can often be found on `PyPI.org <https://pypi.org/>`_ and
downloaded to your local environment using a tool called ``pip3``.

For example, if you wanted to download the
`BioPython <https://pypi.org/project/biopython/>`_ library (a popular library for
biological data analysis) and use it in your Python code, you would first create a 
virtual environment in VSCode's integrated terminal:

.. code-block:: bash

   [mbs-337]$ python3 -m venv myenv
   [mbs-337]$ source myenv/bin/activate
   (myenv) [mbs-337]$ pip3 install biopython
   Collecting biopython
      Downloading ...
   Installing collected packages: numpy, biopython
   Successfully installed biopython-x.xx numpy-x.x.x

.. note::

   **Virtual environments** are isolated Python environments that allow you to
   install packages without affecting the system Python installation. This is 
   the recommended way to manage Python packages. After activating a virtual 
   environment you'll see ``(myenv)`` in your prompt, and you can safely use 
   ``pip3 install`` to install new Python packages to this virtual environment.

   To deactivate the virtual environment later, simply type ``deactivate``. 

Now we can use the BioPython library in our Python code:

.. code-block:: python3
   :linenos:

   from Bio.Seq import Seq

   dna_sequence = Seq("ATGCGATCGATCG")
   print(type(dna_sequence))
   print(dna_sequence)
   print(dna_sequence.transcribe())

Before running this, let's break down what's happening:

 * **Line 1**: Imports the ``Seq`` class from BioPython's ``Bio.Seq`` library. The ``Seq`` class is designed to work with biological sequences (DNA, RNA, proteins).
 * **Line 3**: Creates a ``Seq`` object from a DNA sequence string. This is similar to how we've created strings and lists, but now we're creating a sequence object. 
 * **Line 4**: The ``Seq`` object can be printed just like a string.
 * **Line 5**: Use the ``.transcribe()`` method to transcribe your DNA into RNA!


.. code-block:: bash

   <class 'Bio.Seq.Seq'>
   ATGCGATCGATCG
   AUGCGAUCGAUCG

You can read more about BioPython `here <https://biopython.org/wiki/Documentation>`_ and about the Seq class `here <https://biopython.org/wiki/Seq>`__.


Exercises
---------

Test your understanding of the materials above by attempting the following exercises.

.. attention::

   **Please complete these exercises without using AI tools like ChatGPT or other code generators.** These exercises are designed to help you practice and build your programming skills. Working through them yourself is the 
   best way to learn. If you get stuck, review the material above, work with a classmate,
   or ask your instructor for help. 

   *Hint*: Here are a few functions and methods that may be useful to you: 
    - ``len()``: returns the number of items in an object
    - ``.count()``: tally how many times a value (or character) appears in a list (or string)
    - ``sum()``: return the sum of a collection of numeric items (such as a list)

**Exercise 1:** Create a list of ~10 different integers. Write a function (using modulus and conditionals) to determine if each integer is even or odd. Print to screen each digit followed by the word ‘even’ or ‘odd’ as appropriate.

**Exercise 2:** Using BioPython's ``Seq`` class, determine the GC content of the following DNA sequence: GAACCGGGAGGTGGGAATCCGTCACATATGAGAAGGTATTTGCCCGATAA

**Exercise 3:** Write a function that calculates the percentage of each base (A, T, G, C) in a DNA sequence. The function should return a dictionary with bases as keys and percentages as values. Test it with a sequence of your choice and print the results formatted to 2 decimal places. 

**Exercise 4:** You are analyzing gene expression data from an RNA-seq experiment comparing control and treatment conditions. You have measured expression levels for three different genes, with three biological replicates per condition. 
Create a dictionary to store expression data for 3 genes, where each gene has control and treatment values as follows:

  - Gene 1: Control values = 10.5, 11.2, 10.8; Treatment values = 25.3, 24.7, 26.1
  - Gene 2: Control values = 8.2, 8.5, 8.0; Treatment values = 12.1, 11.8, 12.5
  - Gene 3: Control values = 15.0, 14.8, 15.2; Treatment values = 18.5, 18.2, 18.8
  
  Then, write a script that:
  
  a) Calculates the mean expression for control and treatment for each gene
  b) Calculates the fold change (treatment mean/control mean) for each gene
  c) Prints the results, and identifies which genes show significant changes (use a threshold of fold change > 2.0 OR fold change < 0.5)

  **Hint**: You may want to try a *nested dictionary*, where the value associated with a key can itself be another dictionary!
  You can also iterate over nested dictionaries using for loops:

.. code-block:: python3

   >>> print(expression_data)
   {'Gene1': {'control': [1], 'treatment': [2]}}
   >>> for gene in expression_data:
   ...      print(gene)
   ...      print(expression_data[gene]['control'])
   Gene1
   [1]

Additional Resources
--------------------

* `The Python Standard Library <https://docs.python.org/3/library/>`_
* `PEP 8 Python Style Guide <https://www.python.org/dev/peps/pep-0008/>`_
* `Python3 environment in a browser <https://www.katacoda.com/scenario-examples/courses/environment-usages/python>`_
* `Jupyter Notebooks in a browser <https://jupyter.org/try>`_
* `BioPython Documentation <https://biopython.org/>`_
