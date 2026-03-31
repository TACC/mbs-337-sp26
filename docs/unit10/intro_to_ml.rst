Introduction to Machine Learning
=================================

Machine Learning (ML) is a field of Computer Science and Artificial Intelligence (AI) that develops 
algorithms to analyze and infer patterns in data. Over the last sevaral decades, ML has been  
applied to a wide array of real-world applications, including:

* Natural language: language translation, predictive text, chatbots
* Image analysis: object detection and classification, image generation
* Recommendation systems: From movies, music and social media feeds, to medical therapies
* Aerospace applications: weather prediction, air traffic management, flight path optimization 
* Other fields: Banking, Farming, Manufacturing, Engineering, etc. 

This section begins our discussion of ML by defining important terms, and discussing key concepts in
the context of real problems. By the end of this section, you should be able to:

* Explain the idea of machine learning and the process used to create and deploy an ML model 
* Give examples of ML models used in real-world problems
* Describe the difference between supervised and unsupervised learning
* Describe the difference between independent and dependent variables, classification and 
  regression models


Basic Idea
----------

The goal with ML is to develop a computer model of a natural process or phenomenon using data.
The idea is that algorithms can discover patterns in existing datasets, and these patterns can 
be encoded in a model which can then by applied to new data. 

There are many examples of ML models. Here are just a few to give you a flavor:

1. Given a string of text, predict the next word. 
2. Given an image, determine if it contains a human face. 
3. Given an image of a home or building from the aftermath of a storm, classify the damage done 
   to the structure as "none", "small", or "large".
4. Given a text description of an image, generate an image that "matches" the description. 
5. Given details about a real estate property, such as address, square footage, number of rooms, 
   etc., predict its market value. 
6. Given an image of a crop, determine if the crop has a disease; similarly, determine if the crop
   requires irrigation. 



.. figure:: ./images/ml_in_biology.png
    :width: 600px
    :align: center

    Machine learning applications in biology, adapted from `Alam et. al, 2025 <https://doi.org/10.1186/s12915-025-02424-3>`_.


At a high level, the process may resemble:

1. Find or collect raw data about the process or function.
2. Prepare the data for model training or fitting. 
3. Train the model using some of the prepared data. 
4. Validate the model using some of the prepared data. 
5. Deploy the model to analyze new data samples.

Each of these steps is itself a complex subfield. Usually when people refer to "machine learning",
they are mostly referring to the development of new techniques for steps 3 and 4 (sometimes the term 
*data science* is used to emphasize the large data collection, curation and management aspects). 

In this section, we will mostly assume the raw data has been collected (step 1). The majority of 
our time will be spent on discussing techniques for steps 3 and 4, but we will also discuss steps 2 
and 5.


Supervised and Unsupervised Learning
------------------------------------

All ML techniques require input data. In supervised learning, the dataset provided to the ML 
algorithm comes with *labels*, that is, the values we wish the ML model to predict, for a set 
of "real" samples. 

For example, if we want to train an ML model to learn to distinguish benign vs malignant tumor
cells, a supervised learning approach would provide the model with a collection of features from
histology images, some that were benign and some that are malignant, as well as the appropriate
label for each type. 


.. figure:: ./images/types_of_ml.png
    :width: 600px
    :align: center

    The main types of machine learning, adapted from `Peng et. al, 2021 <https://doi.org/10.3389/fphar.2021.720694>`_.



By contrast, with unsupervised learning the ML model is trained with data, but the data do not
contain labels. Without labels, the ML model must "learn" patterns in the abstract. A major approach
in unsupervised learning is the idea of *clustering*, that is, grouping samples that share
commonalities together. 

For example, given a large number of images, some that contain a face and some that contain
landscapes, an unsupervised learning algorithm may be able to cluster the set of images with faces
together based on their similarities. Images with faces will have an oval shape (the face) with
smaller ovals for eyes, a nose in the middle and lips, while the landscape images will not have any
of these features. 

Unsupervised learning techniques are powerful because they do not require labelling, which can be a
time-consuming process, at best, and one that requires an expert to distinguish different label
values (e.g., the difference between a benign and malignant tumor). 

Nevertheless, due to time constraints we will focus on supervised learning in this section.


Model Variables, Classification, and Regression
-----------------------------------------------

We can further categorize **supervised learning** models as **classification** or **regression**
models. To understand the distinction, it is helpful to first introduce independent and dependent 
variables. 

In an ML setting, the *dependent variable* is the value the model is trying to predict, and 
the *independent variables* are the values the model is using to predict the dependent variable. 

Continuing with our examples above, we can identify the independent and dependent variables as 
follows:

1. Given a string of text, predict the next word. 

   *=> The text string is the independent variable and the next word is the dependent variable.*

2. Given an image, determine if it contains a human face. 

   *=> The image is the independent variable and whether it contains a face is the dependent
   variable.*

3. Given an image of a home or building from the aftermath of a storm, classify the damage done 
   to the structure as "none", "small", or "large".

   *=> The image is the independent variable and the damage label ("none", "small", or "large") is
   the dependent variable.*

4. Given a text description of an image, generate an image that "matches" the description. 

   *=> The text description is the independent variable and the image is the dependent variable.*

5. Given details about a real estate property, such as address, square footage, number of rooms, 
   etc., predict its market value. 

   *=> The property details (address, square footage, etc.) are the independent variables and the
   market value is the dependent variable.*

6. Given an image of a crop, determine if the crop has a disease; similarly, determine if the crop
   requires irrigation. 

   *=> The image is the independent variable and the labels (disease or no disease, irrigation or no
   irrigation) are the dependent variables.*

.. attention::

   **Exercise:** What would the data types (i.e., ``str``, ``bool``, etc.) be for 
   the independent and dependent variables in each of the examples above?


Classification and Regression
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When plotting independent and dependent variables, by convention, we put independent variables on
the *x* axis and dependent variables on the *y* axis. 

Now that we understand independent and dependent variables, we can define classification and 
regression models. *Classification models* involve dependent variables that take a finite set of 
values. We call such dependent variables *categorical* or *discrete*.

A particular case worth noting is the so-called *Boolean classifiers*, which try to
predict dependent variables that contain just two possible values. The name comes from the 
fact that the dependent variable can be modeled with a Boolean data type.

Example 2 above is an example of a Boolean classifier. The dependent variable -- whether the image
contains a face -- can be represented by a boolean variable (True or False). 

Similarly, example 3 is a classifier with 3 possible values ("none", "small", or "large").

By contrast, a *regression model* predicts a dependent variable that take infinitely many 
values. Example 5 provides an example of a regression model -- the market values for real estate
properties are dollar amounts that are unbounded (in practice, they are bounded by very large 
values but it can simplify our thinking to consider them unbounded).

.. attention::

   **Exercise:** In each of the following examples, decide whether the 
   ML problem is a supervised learning or unsupervised learning problem. For the supervised learning, 
   additionally decide whether the problem is a classification or regression problem.   

   1. Given an image of a tumor, determine whether the tumor is cancerous or benign. 
   2. Given a DNA oligo sequence, predict the melting point.
   3. Given an image of animal wildlife, determine the species of animal(s). 


Additional Resources
--------------------

* This documentation is adapted from: 
  `COE 379L: Software Design For Responsible Intelligent Systems <https://coe-379l-sp24.readthedocs.io/en/latest/index.html>`_
