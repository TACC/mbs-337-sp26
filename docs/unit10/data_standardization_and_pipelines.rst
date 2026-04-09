Data Standardization And Pipelines
==================================

In this section, we motivate and introduce additional data pre-processing techniques that involve 
transforming the input dataset before fitting our model. We also introduce the ``Pipeline`` 
class from SciKit-Learn for chaining multiple transformations together. By the end of this section,
you should be able to: 

* Apply data pre-processing techniques that tranform the dataset such as standardization
* Identify when to use pre-processing techniques
* Implement the SciKit-Learn ``Pipeline`` abstraction to conduct multi-step data analysis


Data Pre-Processing: Motivation 
--------------------------------

Most of the data analysis methods from earlier in this section assumed that the variables were on a
similar numeric scale to one another. Moreover, the algorithms for fitting the models we have looked
at mostly work by minimizing a cost function using an algorithm like gradient descent. The partial
derivatives appearing in the gradient computation are sensitive to the (changes of) actual values of
the independent variables. In practice, several issues can arise: 

1. Variables on vastly different scales make exploratory data analysis more difficult; for example, 
   tools such as heatmap and visualizations like plots become harder to use. 
2. One variable can have much larger values than the others and it can dominate the cost function,
   in which case the other variables wouldn't contribute to the fitting as much. 
3. Datasets containing continuous variables with large values and/or variance can make the 
   convergence of optimization algorithms take much longer. 

The idea, at a high-level, is to transform the column variables to put them on the same numeric
scale; for example, between 0 and 1; -1 and 1 or between :math:`min` and :math:`max` for two
constants, :math:`min, max`. However, care is needed for the following reasons: 

1. Not every pre-processing method is applicable to every variable/dataset. For example, variables 
   with a small number of very significant outliers can be skewed with techniques that use averages 
   while the structure of sparse variables would typically be lost if one attempted to center it at, 
   say 0. More generally, the techniques we will look at in this module **apply to continuous 
   variables**. Categorical variables are often treated with different methods, such as one-hot 
   encoding, which we have discussed previously. 
2. The parameters of a pre-processing step should be computed on **only the training** data (i.e., 
   after performing the train-test split) so as to not "leak" information from the test set.
   However, it is very important to apply the pre-processing to the test set before predicting;
   otherwise, the model performance will suffer. 

In addition to improving the overall modularity and reuse of our code, the ``Pipeline`` class will 
help in particular with point 2) above, as it will ensure the same pre-processing is applied to the
test data before predict is called. 


Data Standardization: Mean Removal and Variance Scaling 
--------------------------------------------------------

*Data standardization*, sometimes also referred to as *z-Score normalization*, is the process 
of transforming a continuous variable to have a mean of zero
and a standard deviation of 1. Mathematically, the procedure is straight-forward: for each 
continuous feature :math:`X_i` in the dataset, and each :math:`x \in X_i` we make the following 
update:

.. math::

  x \rightarrowtail (x - mean(X_i)) / std(X_i)

where:

 * :math:`mean(X_i)` is the mean of the column, :math:`X_i`
 * :math:`std(X_i)` is the standard deviation of the column, :math:`X_i`

It's clear that the updated values in the :math:`X_i` column have mean 0 and standard deviation 1. 

Applying data standardization to continuous columns in a dataset can be an important 
pre-processing step when the column variables have a normal distribution -- for example, not sparse,
and no significant outliers. 

*When to Use*: When the dataset is normally distributed (or close to it). 

StandardScaler in SciKit-Learn 
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The ``StandardScaler`` class from the ``sklearn.preprocessing`` module provides a convenience class
that implements data standardization. The classes in ``preprocessing`` module that perform 
transformations on data are all used in the following way:

1. Instantiate an instance of the class 
2. Fit the instance to the training data using the ``.fit()`` function. 
3. Apply the transformation to a dataset using the ``.transform()`` function. 

Note that we always apply the ``fit()`` to the **training data** to "learn" the scaling parameters 
(in this case the mean and standard deviation). We never apply it to test data, as this would 
cause our model to be fit in part based on the test data. 

.. warning:: 

    Using test data to fit the Scaler can lead to overly optimistic performance estimates. 
    A simple rule to remember is this: *Never call fit() on the test data.*

Let's see an example using our Iris data set:

.. code-block:: python
   :linenos:

   from sklearn import datasets
   import numpy as np
   iris = datasets.load_iris()

   from sklearn.model_selection import train_test_split
   X = iris.data
   y = iris.target
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.3, random_state=1)

   # reminder of what the columns contain
   print(iris.feature_names)
   print(X_train[:10])

   # print the column means and standard deviations
   print(f'mean by column:   {np.mean(X_train, axis=0)}')
   print(f'stddev by column: {np.std(X_train, axis=0)}')

The output should be similar to:

.. code-block:: text

   ['sepal length (cm)', 'sepal width (cm)', 'petal length (cm)', 'petal width (cm)']
   array([[7.7, 2.6, 6.9, 2.3],
          [5.7, 3.8, 1.7, 0.3],
          [5. , 3.6, 1.4, 0.2],
          [4.8, 3. , 1.4, 0.3],
          [5.2, 2.7, 3.9, 1.4],
          [5.1, 3.4, 1.5, 0.2],
          [5.5, 3.5, 1.3, 0.2],
          [7.7, 3.8, 6.7, 2.2],
          [6.9, 3.1, 5.4, 2.1],
          [7.3, 2.9, 6.3, 1.8]])
   mean by column:   [5.8        3.03809524 3.73809524 1.19047619]
   stddev by column: [0.84052138 0.4168775  1.78012204 0.77931885]

We see that prior to standardization the columns each have a positive mean and non-normal standard
deviation. Next, transform the data using the standard Scaler:

.. code-block:: python

   from sklearn.preprocessing import StandardScaler
   
   # step 1 -- Instantiate the Scaler
   iris_scaler = StandardScaler()
   # step 2 -- fit the Scaler to the training data 
   iris_scaler.fit(X_train)
   # step 3 -- apply the transformation; in this case, we apply it to the training data. 
   X_train_scaled = iris_scaler.transform(X_train)
   
   # print the column means and standard deviations after transformation
   print(f'scaled mean by column:   {np.mean(X_train_scaled, axis=0)}')
   print(f'scaled stddev by column: {np.std(X_train_scaled, axis=0)}')

The output should now be similar to:

.. code-block:: text

   scaled mean by column:   [-8.47998920e-16 -1.81442163e-15  2.11471052e-17 -5.96348368e-16]
   scaled stddev by column: [1. 1. 1. 1.]

We see that the mean of the dataset after applying the transformation is (essentially) 0 
and the standard deviation is 1. 

.. note:: 

   Even though the above method works fine, we recommend using the ``Pipeline`` class 
   described at the end of this section when combining data preprocessing with model 
   training. 



Robust Scalers 
~~~~~~~~~~~~~~

When the dataset contains outliers that deviate significantly from the mean, using standardization
could result in worse performance because the outliers could dominate the mean/variance and crush
the signal. 

In these cases, a robust Scaler based on different statistical methods, such as IQR, can be used
instead. With a robust Scaler, the median is removed, and scaling is performed based on some
percentage range. 

*When to Use*: When the dataset contains outliers that deviate significantly from the mean. 

MaxAbs Scaler 
~~~~~~~~~~~~~

The last Scaler we will mention is the ``MaxAbsScaler``, short for "maximum absolute" scaler. 
This scaler uses the maximum absolute value of each feature to scale the values of that 
feature (i.e., the maximum absolute values of each feature after transformation will be 1). 
Note that it does not attempt to shift/center the data, so if a feature is sparse 
(i.e., consists mostly of 0s), the data "spareness" structure will not be destroyed. 

Note also that this scaler does not reduce the effect of outliers. 

*When to Use*: When the dataset contains sparse data. 


Pipelines 
---------

Example 1: Data Standardization
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We now have a dilemma - the training data was scaled using the ``StandardScaler`` but the test data
was not. This will lead to poor performance when we attempt to predict on the test data. (But remember -
this was by design! The test data should not be used to fit the Scaler).

To avoid this issue, we can use a ``Pipeline`` to ensure that all preprocessing steps are applied
consistently to both the training and test data. A pipeline bundles together a sequence of data
processing steps, including scaling, and allows us to treat the entire sequence as a single object.

We can create a pipeline for our Iris dataset by adding the following code:

.. code-block:: python

   from sklearn.pipeline import Pipeline
   from sklearn.linear_model import SGDClassifier

   # create a pipeline
   iris_pipeline = Pipeline([
       ('scaler', StandardScaler()),
       ('classifier', SGDClassifier())
   ])

   # fit the pipeline to the training data
   iris_pipeline.fit(X_train, y_train)

   # make predictions on the test data
   y_pred = iris_pipeline.predict(X_test)

   # evaluate the model
   from sklearn.metrics import accuracy_score
   print(f'Accuracy: {accuracy_score(y_test, y_pred)}')

The pipeline itself combines two steps: 1) scaling the data using the ``StandardScaler`` and 2) 
fitting a linear classifier using stochastic gradient descent. When we call ``fit()`` on the 
pipeline, it will first fit the scaler to the training data, then apply the transformation to the 
training data, and finally fit the classifier to the scaled training data. When we call 
``predict()`` on the pipeline, it will apply the same scaling transformation to the test data before 
making predictions with the classifier. This ensures that the test data is preprocessed in the same 
way as the training data, which can lead to better performance.

Importantly, the pipeline itself can be pickled, just like other sklearn objects, which allows us
to save the entire sequence of transformations and the model in one step. For example, consider the 
following code blocks:


.. code-block:: python

    import pickle
    
    # save the pipeline to disk
    with open('iris_pipeline.pkl', 'wb') as f:
        pickle.dump(iris_pipeline, f)
    
    # load the pipeline from disk
    with open('iris_pipeline.pkl', 'rb') as f:
        loaded_pipeline = pickle.load(f)


EXERCISE
~~~~~~~~

Let's go through the process of putting the above Iris classifier into production.

1. Normalize the original data using a ``StandardScaler``
2. Fit a linear classifier to the normalized data using stochastic gradient descent (SGD)
3. Save the pipeline to disk using ``pickle``
4. Load the pipeline from disk and use it to make predictions on non-normalized sample data,
   e.g. ``[5.1, 3.5, 1.4, 0.2]``
5. Write a production-ready script (following Python best practices) that takes command line 
   arguments for the input data (sepal length, sepal width, petal length, petal width) and outputs
   the predicted class


Example 2: One-Hot-Encoding
~~~~~~~~~~~~~~~~~~~~~~~~~~~

We have one more dilemma to solve: How do you perform inference on previously-unseen sample
data when the training data has been one-hot-encoded? As we saw in the Exercise from the previous 
lesson, attempting to one-hot-encode a sample absent the training data will lead to too-few
columns. And, we don't want to have to import all of the original training data every time we 
need to encode sample data. 

To solve this, we can create a pipeline that includes the one-hot-encoding step as well as the
model fitting step. This way, when we call ``predict()`` on the pipeline, it will automatically
apply the correct one-hot-encoding transformation to the sample data before making predictions. This ensures
that the sample data is preprocessed in the same way as the training data, which will avoid the 
errors we saw previously about the mismatch in number of columns

Here is an example of how to create such a pipeline (this code has been adapted from the code
in the previous lesson):

.. code-block:: python
    :linenos:
    :emphasize-lines: 9-11,34-37,41,45

    import random
    import pandas as pd 
    from ucimlrepo import fetch_ucirepo
    from sklearn.model_selection import train_test_split
    from sklearn.metrics import classification_report
    import tensorflow as tf 
    from tensorflow.keras import Sequential
    from tensorflow.keras.layers import Input, Dense
    from sklearn.preprocessing import OneHotEncoder
    from sklearn.pipeline import Pipeline
    from scikeras.wrappers import KerasClassifier
    
    tf.random.set_seed(123)
    random.seed(123)
    
    # Fetch dataset
    mushroom = fetch_ucirepo(id=73)
    X = mushroom.data.features
    y = mushroom.data.targets
    X_clean = X.drop(columns=['stalk-root'])
    y_encoded = y['poisonous'].map({'p': 1, 'e': 0})

    # Create model with sequential API
    model = Sequential([
        Input(shape=(112,)),
        Dense(10, activation='relu'),
        Dense(1, activation='sigmoid')
    ])
    
    # Compile the model with appropriate settings for binary classification
    model.compile(optimizer='adam', loss='binary_crossentropy', metrics=['accuracy'])
    
    # Create a Pipeline
    mushroom_pipeline = Pipeline([
        ('encoder', OneHotEncoder(handle_unknown='ignore')),
        ('classifier', KerasClassifier(model=model, validation_split=0.2, epochs=5, batch_size=32, verbose=2))
    ])
    
    # Split the dataset into training and testing sets
    X_train, X_test, y_train, y_test = train_test_split(
        X_clean, y_encoded, test_size=0.3, stratify=y_encoded, random_state=123
    )
    
    # Train the model with the specified parameters
    mushroom_pipeline.fit(X_train, y_train)
    
    # Make predictions on the test data
    y_pred = mushroom_pipeline.predict(X_test)
    y_pred_final = (y_pred > 0.5).astype(int)
    print(classification_report(y_test,y_pred_final, digits=4))


Some important notes about this code: 

1. Instead of using the pandas ``get_dummies()`` method to one-hot-encode the data, we use the
   ``OneHotEncoder`` class from SciKit-Learn. This is because the ``Pipeline`` class expects all
   transformations to be implemented as classes with ``fit()`` and ``transform()`` methods. The
   ``OneHotEncoder`` class provides this functionality, while the ``get_dummies()`` method does not.
2. We set the ``handle_unknown`` parameter of the ``OneHotEncoder`` to "ignore" to ensure that if
   we encounter a category in the sample data that was not present in the training data, the encoder
   will simply ignore it instead of throwing an error. This is important for ensuring that our
   pipeline can handle previously-unseen sample data without crashing.
3. We use the ``KerasClassifier`` wrapper from the ``scikeras.wrappers`` module to integrate our
   Keras model into the SciKit-Learn pipeline. This allows us to treat the Keras model as a standard
   estimator that can be used in the pipeline alongside other transformations.
4. Notice when we do the ``train_test_split()`` we are splitting the original X data which has not
   yet been one-hot-encoded. This is because the one-hot-encoding will be applied as part of the
   pipeline, so we want to split the original data before applying any transformations. 
5. Finally, call the ``.fit()`` and ``.predict()`` methods on the pipeline itself, rather than on
   the individual components.

Finally, we can save this pipeline to disk using ``pickle`` and load it later to make predictions 
on new sample data, just like we did with the previous pipeline. This way, we can ensure that all 
of the necessary transformations are applied consistently to any new data we want to classify.

.. code-block:: python

    import pickle
    
    # save the pipeline to disk
    with open('mushroom_pipeline.pkl', 'wb') as f:
       pickle.dump(mushroom_pipeline, f)
    
    # load the pipeline from disk
    with open('mushroom_pipeline.pkl', 'rb') as f:
       loaded_pipeline = pickle.load(f)


.. note::

    You may still need to handle some edge cases when taking sample data from the user. For example,
    in our original code we drop the "stalk-root" column because it contains missing values. If a user 
    tries to classify a sample that includes a value for "stalk-root", what will happen?


EXERCISE
~~~~~~~~

Let's go through the process of putting the Mushroom classifier into production.


1. Create a pipeline for the Mushroom dataset that one-hot-encodes the data and fits a Keras
   Sequential model.
2. Train the pipeline on the training data and evaluate it on the test data.
3. Save the pipeline to disk using ``pickle``.
4. Load the pipeline into a new script and use it to make predictions on the following sample
   data, provided in JSON:

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


Additional Resources
--------------------

* Adapted from: 
  `COE 379L: Software Design For Responsible Intelligent Systems <https://coe-379l-sp24.readthedocs.io/en/latest/index.html>`_
* `SciKit-Learn: Scalers <https://scikit-learn.org/stable/modules/preprocessing.html>`_
* `SciKit-Learn: Pipelines <https://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html>`_
* `SciKit-Learn: Custom Estimators <https://scikit-learn.org/stable/developers/develop.html>`_
