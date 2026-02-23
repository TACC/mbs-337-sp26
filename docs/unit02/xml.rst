XML Reference
================

This reference guide is designed to introduce you to XML syntax. XML (Extensible
Markup Language) was designed as a way to store and transport structured data
across different systems, especially the Internet. XML is widely used in bioinformatics
to standardize the representation and exchange of complex biological data, particularly
for datasets with intricate, hierarchical structures. 

XML Basics
----------

Much like JSON, XML can be used to store datasets in a dictionary-esque
format of key:value pairs. However, there are a few key differences between XML
and JSON:

1. XML is typeless, essentially everything is a string
2. XML documents support comments
3. XML documents require exactly one "root" element, so the data structure of
   a **dictionary with one key, whose value is a list of dictionaries** needs a slight
   modification in order to be valid JSON (as we will see below)

A valid XML document takes the form:

.. code-block:: xml

   <data>
       <key1>value1</key1>
       <key2>value2</key2>
   </data>

Keys always come as a pair of start-tag (``<key1>``) and end-tag (``</key1>``)
markups surrounding a plain text value (``value1``). In the above example, the
root-level key ``data`` was required to make this valid XML. Without it, the
keys ``key1`` and ``key2`` would be at the same root level, which is invalid.
The same data shown above in JSON format would appear as:

.. code-block:: json

   {
     "data": {
       "key1": "value1",
       "key2": "value2"
     }
   }


Things get a little tricky with our favorite data structure, a **dictionary with
one key, whose value is a list of dictionaries**. This is because of the way
XML represents lists of dictionaries. Consider the following snippet of JSON data
containing protein information:

.. code-block:: json

   {
   "protein_entries": [
      {
         "proteinName": "Myoglobin",
         "organism": "Homo sapiens",
         "className": "oxygen carrier",
         "mass": 17184,
         "length": 154
      },
      {
         "proteinName": "Hemoglobin subunit beta",
         "organism": "Homo sapiens",
         "className": "oxygen carrier",
         "mass": 15998,
         "length": 147
      }

If we try to translate this directly to XML, it would take the form:

.. code-block:: xml

    <protein_entries>
            <proteinName>Myoglobin</proteinName>
            <organism>Homo sapiens</organism>
            <className>oxygen carrier</className>
            <mass>17184</mass>
            <length>154</length>
    </protein_entries>
    <protein_entries>
            <proteinName>TP53</proteinName>
            <organism>Homo sapiens</organism>
            <className>oxygen carrier</className>
            <mass>15998</mass>
            <length>147</length>
    </protein_entries>

The ``protein_entries`` key appears multiple times at the root level, once for each
element in the list. In XML, you cannot have multiple roots, even if it is the
same root repeated more than once. You need exactly one root only. A simple
trick to fix this is to create a new dictionary with one key, e.g. "data", whose
value is the other dictionary. Doing so would slightly change the XML to a valid
format:

.. code-block:: xml

   <data>
      <protein_entries>
            <proteinName>Myoglobin</proteinName>
            <organism>Homo sapiens</organism>
            <className>oxygen carrier</className>
            <mass>17184</mass>
            <length>154</length>
      </protein_entries>
      <protein_entries>
            <proteinName>TP53</proteinName>
            <organism>Homo sapiens</organism>
            <className>oxygen carrier</className>
            <mass>15998</mass>
            <length>147</length>
      </protein_entries>
   </data>

.. note::

   Check out the list of uniprot proteins we worked with in the JSON and CSV sections, but
   now in XML format `here <https://raw.githubusercontent.com/kbeavers/mbs-337-sp26/refs/heads/main/docs/unit02/sample-data/uniprot_proteins_simple.xml>`_.


Read XML from File
------------------

Here we will focus on the "document object model" for parsing XML, which means
we will read in one XML document and parse the entire thing as a whole. (This
works for reasonably small files that can fit in memory).

Note that the Python3 standard library has an XML module, but it does not have
a method for transforming XML objects to dictionaries. Since most of what we do
in this class uses JSON and dictionaries, let's instead use the ``xmltodict``
Python module which works directly in dictionary space.

.. warning::

   Install the ``xmltodict`` library before proceeding. Make sure your virtual
   environment is activated:

   .. code-block:: console

      (myenv) [mbs-337]$ pip3 install xmltodict


You can read in an XML file (e.g., protein data) and store it as a dictionary as follows:

.. code-block:: python3

   import xmltodict

   with open('Protein_List.xml', 'r') as f:
       data = xmltodict.parse(f.read())

Then to access the data within that dictionary, remember to include an extra key
for the root-level, which we added in to make valid XML. For example, you could
call out the first protein in the list with the following:

.. code-block:: python3

   print(data['data']['protein_entries'][0])


.. note::

   XML tags cannot contain certain characters like parentheses ``()``, spaces, or
   special symbols. If your data has keys with these characters, you'll need to
   modify them. For example, a key like ``mass (g)`` would need to be changed to
   something like ``mass_g`` or ``mass_grams``. Don't be surprised when working with
   datasets if you have to make manual modifications to the data in order to make
   it valid in a particular format.


Write XML to File
-----------------

As mentioned above, a dictionary must have exactly one "root" element in order
to write valid XML. The following example below assembles a dictionary with
multiple keys at the root level ("dataset_id", "title", "keywords"). In fact the
following code will yield an error:


.. code-block:: python3
   :linenos:

   import xmltodict

   data = {}
   data['accession'] = 'PRJNA1412539'
   data['id'] = '1412539'
   data['title'] = 'Transposon-insertion sequencing uncovers nlpD as the essential gene for intracellular persistence and infectivity of Salmonella'
   data['dataType'] = 'Raw sequence reads'

   with open('dataset.xml', 'w') as o:
       o.write(xmltodict.unparse(data, pretty=True))

Error:

.. code-block:: text

   ValueError: Document must have exactly one root.

To get this to work, you need to modify the above script to create a new
dictionary, e.g. "root", with exactly one key, e.g. "data", whose value is the
entire ``data`` dictionary:

.. code-block:: python3
   :linenos:
   :emphasize-lines: 9-10,13

   import xmltodict

   data = {}
   data['accession'] = 'PRJNA1412539'
   data['id'] = '1412539'
   data['title'] = 'Transposon-insertion sequencing uncovers nlpD as the essential gene for intracellular persistence and infectivity of Salmonella'
   data['dataType'] = 'Raw sequence reads'

   root = {}
   root['data'] = data

   with open('dataset.xml', 'w') as o:
       o.write(xmltodict.unparse(root, pretty=True))

Output:

.. code-block:: xml

   <?xml version="1.0" encoding="utf-8"?>
   <data>
      <accession>PRJNA1412539</accession>
      <id>1412539</id>
      <title>Transposon-insertion sequencing uncovers nlpD as the essential gene for intracellular persistence and infectivity of Salmonella</title>
      <dataType>Raw sequence reads</dataType>
   </data>


Additional Resources
--------------------
* Many of the materials in this module were adapted from `COE 332: Software Engineering & Design <https://coe-332-sp26.readthedocs.io/en/latest/unit02/xml.html>`_
* `The xmltodict Library <https://github.com/martinblech/xmltodict>`_
* `XML Linter <https://xmllint.com/>`_
* Many biological databases provide XML formats — check out NCBI's XML formats for gene and protein data
