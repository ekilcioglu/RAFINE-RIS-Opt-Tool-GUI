Installation Guide
##################

This guide explains how to set up the necessary dependencies to run our RIS optimization tool. Since the GUI source code is provided on GitHub publicly, only required Python packages need to be installed.

Prerequisites
=============

Ensure you have **Python 3.8+** installed. You can check your Python version by running:

.. code-block:: sh

   python --version

Installing Required Packages
============================

To install all necessary dependencies, run the following command:

.. code-block:: sh

   pip install numpy matplotlib scikit-learn tensorflow

Since Sionna ray-tracing tool does not have the RIS support yet for their new v1.0.x versions, our RIS optimization tool uses the Sionna version 0.19.2, which is working stable and the following command should be run for that:

.. code-block:: sh

   pip install sionna==0.19.2

Required Modification in Sionna Source Code
===========================================

Before running the GUI, you need to manually modify Sionna's source code to enable coverage map editing.

Specifically, add the following method to the file **`sionna/rt/coverage_map.py`** inside your Python site-packages directory. This method allows updating the internal `path_gain` tensor of a coverage map object:

.. code-block:: python

   def set_value(self, new_value):
       """
       Sets a new value for the coverage map.

       Parameters:
       -----------
       new_value : tf.Tensor
           A tensor of shape [num_tx, num_cells_y, num_cells_x] representing the new coverage map values.

       Raises:
       -------
       ValueError
           If the shape of new_value doesn't match the existing coverage map dimensions.
       """
       if hasattr(self, '_value'):
           if new_value.shape != self._value.shape:
               raise ValueError(f"Shape mismatch. Expected shape {self._value.shape}, but got {new_value.shape}")
       
       self._path_gain = tf.cast(new_value, self._rdtype)

This method is required by certain GUI functionalities that modify or visualize the coverage map more clearly by masking wall reflections.

Package Explanations
====================

- **numpy**: Needed for numerical operations and data manipulation.

- **matplotlib**: Required for plotting and visualization.

- **scikit-learn**: Provides KMeans clustering for target point selection.

- **sionna**: Used for ray-tracing simulation and RIS modeling.

- **tensorflow**: Required for `sionna`, handles deep learning computations.

Notes
=====

- `tkinter` is included with Python, so no separate installation is required.

- If you plan to use GPU acceleration, install TensorFlow with GPU support:

  .. code-block:: sh

     pip install tensorflow tensorflow-gpu

Once the installation is complete, you should be able to run the GUI with the file "GUI.ipynb" using Jupyter Notebook.
