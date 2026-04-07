From Training to Production
===========================

To briefly recap, we have written code that downloaded and prepared data, trained a model,
then validated the performance of model. However the code that we wrote is still somewhat
ephemeral - the model only existed as long as we had the Jupyter notebook running. How do
we put that model into a production environment for others to use with their own data?

After going through this section, you should be able to:

* Describe the general process of MLOps from training to inference in production
* Export models to binary Python objects using ``pickle`` or Tensorflow's ``model.save()``
* Import previously-exported models into new Python scripts


Model Persistence
-----------------

Recall that, at a high level, the use of ML involves the following process:

1. Find or collect raw data about the process or function
2. Prepare the data for model training or fitting
3. Train the model using some of the prepared data
4. Validate the model using some of the prepared data
5. Deploy the model to analyze new data samples

We've look at pretty much all of these steps except for the last one which involves the topic 
of machine learning operations, or MLOps. In practice, we need a method for saving and deploying 
a model that has already been trained to an application where it can analyze new data. We 
certainly don't want to have to retrain the model every time we start our application, for several
reasons: 

1. Model training requires data, which can be large and difficult to ship with our application 
2. Training can be a time-consuming process
3. The training process might not be possible/reproducible on every device where we wish to deploy 
   our application

All of those reasons motivate the need to be able to save and load models that have already been
trained. 

Here, we will look at a first method for saving and loading models to a file based on the Python
``pickle`` module, which is part of the standard library. The method we mention has the advantage
that it is simple and can be used with many Python objects, not just models. However, it also comes
with security risks, which we will mention. 


The ``pickle`` Module
---------------------

The ``pickle`` module is part of the Python standard library and provides functions for serializing 
and deserializing Python objects to and from a bytestream. 

The process of converting a Python object to a bytestream is referred to as *pickling the object*,
and the reverse process of taking a bytestream and converting it back to a Python object is called
*unpickling*. 

Once a Python object has been converted to a bytestream with pickle, the bytestream can then be
written to a file. Later, we can read the bytes back out of the file and reconstitute the original
Python object. 

Many Python objects can be pickled, including the following: 

* builtin constants (True, False, None) 
* strings, bytes and bytearrays 
* *some* classes and class instances (specifically, the ones that implement ``__getstate__()``)
* lists, dictionaries, and tuples of picklable objects. 

In general, the models we have looked at from sklearn can be pickled. 

Using the pickle module is straightforward, and it provides a similar API to that of JSON. 
We use the following methods for serialization: 

* ``pickle.dumps(obj)`` converts the Python object, ``obj``, to a bytestream. 
* ``pickle.dump(obj, file)`` converts the Python object, ``obj``, to a bytestream and writes it to ``file``. 

And similarly, for deserializing:

* ``pickle.loads(bytes)`` converts the ``bytes`` object to a Python object. 
* ``pickle.load(file)`` reads the contents of ``file`` and converts the bytes to a Python.

Of course, the ``load()`` and ``loads()`` functions will fail if the bytes read in were not originally 
created by the pickle module. 


Practical Example - Iris Classifier
-----------------------------------

Let's see this in action. Suppose we have just trained a linear classifier for the Iris data: 

.. code-block:: python
    :linenos:

    from sklearn import datasets
    from sklearn.model_selection import train_test_split
    from sklearn.linear_model import SGDClassifier
    from sklearn.metrics import accuracy_score

    iris = datasets.load_iris()
    X = iris.data
    y = iris.target
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, stratify=y, random_state=1)

    clf = SGDClassifier(loss="perceptron", alpha=0.01, random_state=1)
    clf.fit(X_train, y_train)
    print(f'accuracy = {accuracy_score(y_test, clf.predict(X_test))}')


We can use pickle to save the model to a file: 

.. code-block:: python

    import pickle
    with open('my_sgdclf.pkl', 'wb') as f:
        pickle.dump(clf, f)

.. tip::

    Use descriptive filenames when naming your pickle file. Consider naming it after the model, the
    source of the training data, the version (Git commit hash) of your code, etc.

Note the use of writing to the file in **binary format** (the ``'wb'`` flag in the call to ``open``). 
This is important - the pickle output is a bytestream so without the ``b``, the write will fail. 

Next, create a brand new Python script to read the model back in from the file. Note we don't need to
re-train the model, but in this case we are pulling the raw Iris data again so we have something
to test:

.. code-block:: python
    :linenos:
    :emphasize-lines: 5,12-13

    from sklearn import datasets
    from sklearn.model_selection import train_test_split
    from sklearn.linear_model import SGDClassifier
    from sklearn.metrics import accuracy_score
    import pickle

    iris = datasets.load_iris()
    X = iris.data
    y = iris.target
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, stratify=y, random_state=1)

    with open('my_sgdclf.pkl', 'rb') as f:
       clf = pickle.load(f)
    print(f'accuracy = {accuracy_score(y_test, clf.predict(X_test))}')


.. note:: 

    Note that in general, Python callables (e.g., functions) *cannot* be pickled. If you need to serialize 
    a callable, consider using the third-party ``cloudpickle`` package instead, available from pypi.



EXERCISE
~~~~~~~~

Write a short Python script that loads in the pre-trained classifier, and classifies a sample
with sepal and petal measurements of ``[5.1, 3.5, 1.4, 0.2]``.


A Note on Security with ``pickle``
-----------------------------------

We need to be very careful when using the ``pickle`` library to load Python objects. It is possible to 
serialize code that could harm your machine when loaded. For that reason, it is recommended that you 
**only** use ``pickle.load()`` and ``pickle.loads()`` on files and bytestreams that you know and trust 
(i.e., that you wrote yourself). As a result, ``pickle`` is not a suitable solution for some cases; 
for example, a web API or service that allows users to upload their own model and execute them on the 
cloud.

.. warning:: 

    Never use pickle to load a bytestream that you did not write yourself. You could do harm to your 
    computer. 


Serializing and Deserializing Tensorflow Models
-----------------------------------------------

The Python ``pickle`` module is great for serializing a sklearn model. However, for serializing a 
Tensorflow model we recommend using the built in ``model.save()`` method. In general, attempting to
use ``pickle`` on Tensorflow models can lead to errors related to model objects not being
pickleable. 


Practical Example - Mushroom Classifier
---------------------------------------

We'll illustrate the techniques in this section using a model trained against the Mushroom dataset.
Recall that dataset consisted of 8,124 samples each with 22 features and a binary classification
(poisonous or edible).

.. code-block:: python
    :linenos:

    import random
    import pandas as pd
    from ucimlrepo import fetch_ucirepo
    from sklearn.model_selection import train_test_split
    from sklearn.metrics import classification_report
    import tensorflow as tf
    from tensorflow.keras import Sequential
    from tensorflow.keras.layers import Input, Dense
    
    tf.random.set_seed(123)
    random.seed(123)
    
    # Fetch dataset
    mushroom = fetch_ucirepo(id=73)
    X = mushroom.data.features
    y = mushroom.data.targets
    X_clean = X.drop(columns=['stalk-root'])
    
    # Encode data
    X_encoded = pd.get_dummies(X_clean)
    y_encoded = y['poisonous'].map({'p': 1, 'e': 0})
    
    # Split the dataset into training and testing sets
    X_train, X_test, y_train, y_test = train_test_split(
        X_encoded, y_encoded, test_size=0.3, stratify=y_encoded, random_state=123
    )
    
    # Create model with sequential API
    model = Sequential([
        Input(shape=(112,)),
        Dense(10, activation='relu'),
        Dense(1, activation='sigmoid')
    ])
    
    # Compile the model with appropriate settings for binary classification
    model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])
    
    # Train the model with the specified parameters
    model.fit(X_train, y_train, validation_split=0.2, epochs=5, batch_size=32, verbose=2)
    
    # Make predictions on the test data
    y_pred = model.predict(X_test)
    y_pred_final = (y_pred > 0.5).astype(int)
    print(classification_report(y_test,y_pred_final, digits=4))


Running the above code should produce output that looks similar to the following: 

.. code-block:: console 

    ... 
    Epoch 1/5
    143/143 - 1s - 6ms/step - accuracy: 0.8709 - loss: 0.3543 - val_accuracy: 0.9569 - val_loss: 0.1458
    Epoch 2/5
    143/143 - 0s - 1ms/step - accuracy: 0.9776 - loss: 0.0964 - val_accuracy: 0.9851 - val_loss: 0.0638
    Epoch 3/5
    143/143 - 0s - 2ms/step - accuracy: 0.9894 - loss: 0.0481 - val_accuracy: 0.9938 - val_loss: 0.0364
    Epoch 4/5
    143/143 - 0s - 2ms/step - accuracy: 0.9949 - loss: 0.0288 - val_accuracy: 0.9982 - val_loss: 0.0230
    Epoch 5/5
    143/143 - 0s - 2ms/step - accuracy: 0.9985 - loss: 0.0186 - val_accuracy: 0.9982 - val_loss: 0.0157
    77/77 ━━━━━━━━━━━━━━━━━━━━ 0s 837us/step
                  precision    recall  f1-score   support
    
               0     0.9968    0.9992    0.9980      1263
               1     0.9991    0.9966    0.9979      1175
    
        accuracy                         0.9979      2438
       macro avg     0.9980    0.9979    0.9979      2438
    weighted avg     0.9980    0.9979    0.9979      2438


It's unlikely that a few more epochs will improve performance. We're over 99% accuracy 
on both the test and validation sets, and the validation accuracy has started to plateau, so 
this seems like a good time to save the model. 

We use the ``model.save()`` function, passing in a file name to use to save the model. I will use 
the simple name ``mushroom_classifier.keras``. It is a good habbit to save the models with a ``.keras`` extension. 

.. code-block:: python3 

   model.save("mushroom_classifier.keras")

There should now be a file, ``mushroom_classifier.keras`` in the same directory as the script you
are running. If we inspect this file, we will see that it is a zip archive and about 34KB: 

.. code-block:: console 

   [mbs337-vm]$ file mushroom_classifier.keras
   mushroom_classifier.keras: Zip archive data, at least v2.0 to extract, compression method=store

.. note:: 

   Keras supports multiple file format versions for saving models. The latest version, v3, will 
   automatically be used whenever the file name passed ends in the ".keras" extension. From the
   official docs:
   
   *"The new Keras v3 saving format, marked by the .keras extension, is a more simple, efficient 
   format that implements name-based saving, ensuring what you load is exactly what you saved, 
   from Python's perspective. This makes debugging much easier, and it is the recommended 
   format for Keras."*

At this point, we can load our model easily from the saved file into a new Python program. To illustrate, 
let's start work in a brand new Python where we will implement the following code:

.. code-block:: python

   import tensorflow as tf 
   model = tf.keras.models.load_model('mushroom_classifier.keras')
   
Let's evaluate our model on the training set to convince ourselves that this is indeed our pre-trained 
model. Again, we will load the original data so we have something to test:


.. code-block:: python
    :linenos:

    import pandas as pd
    from ucimlrepo import fetch_ucirepo
    from sklearn.model_selection import train_test_split
    import tensorflow as tf
    
    # Fetch dataset
    mushroom = fetch_ucirepo(id=73)
    X = mushroom.data.features
    y = mushroom.data.targets
    X_clean = X.drop(columns=['stalk-root'])
    
    # Encode data
    X_encoded = pd.get_dummies(X_clean)
    y_encoded = y['poisonous'].map({'p': 1, 'e': 0})
    
    # Split the dataset into training and testing sets
    X_train, X_test, y_train, y_test = train_test_split(
        X_encoded, y_encoded, test_size=0.3, stratify=y_encoded, random_state=123
    )
    
    # Load the pre-trained model and evaluate
    model = tf.keras.models.load_model('mushroom_classifier.keras')
    print( model.evaluate(X_test, y_test, batch_size=32) )

The output from running this code should looks similar to:

.. code-block:: console 

    ... 
    77/77 ━━━━━━━━━━━━━━━━━━━━ 0s 987us/step - accuracy: 0.9979 - loss: 0.0171 
    [0.017112771049141884, 0.9979491233825684]

Indeed, we get 99% accuracy on the training set. We're ready to deploy this model publicly. 

.. warning:: 

   Be very careful about the version of tensorflow you use to save the model and the version used 
   to load the model. Changing major versions (e.g., tensorflow v1 to v2) can cause the model to 
   fail to load, and even changing from 2.15 to 2.16 because 2.16 introduced a new major version 
   of Keras (v3). The safest approach is always to use identical versions when saving and loading. 


EXERCISE
~~~~~~~~

**Thought Experiment:** Imagine you have built a dashboard for classifying mushrooms as edible or
poisonous. What does the interface look like for a user to input data? How does the backend code
capture that input? 

Write a short Python script that loads in the pre-trained mushroom classifier, and classifies a
sample with features:

.. code-block:: text

    {
      "cap-shape": "x",
      "cap-surface": "s",
      "cap-color": "n",
      "bruises": "t",
      "odor": "p",
      "gill-attachment": "f",
      "gill-spacing": "c",
      "gill-size": "n",
      "gill-color": "k",
      "stalk-shape": "e",
      "stalk-root": "e",
      "stalk-surface-above-ring": "s",
      "stalk-surface-below-ring": "s",
      "stalk-color-above-ring": "w",
      "stalk-color-below-ring": "w",
      "veil-type": "p",
      "veil-color": "w",
      "ring-number": "o",
      "ring-type": "p",
      "spore-print-color": "k",
      "population": "s",
      "habitat": "u"
    }


What challenges exist in performing inference on one stand-alone sample? 


Additional Resources
--------------------

* Adapted from: 
  `COE 379L: Software Design For Responsible Intelligent Systems <https://coe-379l-sp24.readthedocs.io/en/latest/index.html>`_
* `Python pickle Docs <https://docs.python.org/3/library/pickle.html>`_
* `Cloudpickle Python Package on Github <https://github.com/cloudpipe/cloudpickle>`_
* `Keras model saving and loading <https://keras.io/api/models/model_saving_apis/model_saving_and_loading/>`_
