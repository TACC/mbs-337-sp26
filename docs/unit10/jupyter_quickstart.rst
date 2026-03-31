Jupyter Quickstart
==================

We will primarily be using Jupyter notebooks to develop and test models for this unit. A summary
of the commands to start and connect to a Jupyter notebook on your class VM can be found below. If
needed, please refer back to the `unit on Jupyter <../unit07/overview.html>`_ for full instructions.


Installation
------------

.. code-block:: console

   [mbs337-vm]$ cd $HOME/mbs-337
   [mbs337-vm]$ source .venv/bin/activate
   (.venv) [mbs337-vm]$ pip3 install jupyter


Configuration File
------------------

.. code-block:: console

   (.venv) [mbs337-vm]$ cd
   (.venv) [mbs337-vm]$ mkdir .jupyter && cd .jupyter
   (.venv) [mbs337-vm]$ wget https://raw.githubusercontent.com/tacc/mbs-337-sp26/main/docs/unit07/scripts/jupyter_config.py
   (.venv) [mbs337-vm]$ ls -l
   total 4
   -rw-rw-r-- 1 ubuntu ubuntu 241 Mar  1 17:01 jupyter_config.py


Set a Password
--------------


.. code-block:: console

   (.venv) [mbs337-vm]$ jupyter notebook password
   Enter password: ********
   Verify password: ********
   [JupyterPasswordApp] Wrote hashed password to /home/ubuntu/.jupyter/jupyter_server_config.json
   (.venv) [mbs337-vm]$ ls -l
   total 12
   -rw-rw-r-- 1 ubuntu ubuntu 241 Mar  1 17:01 jupyter_config.py
   -rw------- 1 ubuntu ubuntu 162 Mar  1 17:36 jupyter_server_config.json
   -rw-rw-r-- 1 ubuntu ubuntu  32 Mar  1 17:36 migrated

Start the Jupyter Notebook/Lab Server
-------------------------------------

.. code-block:: console

   (.venv) [mbs337-vm]$ cd $HOME/mbs-337
   (.venv) [mbs337-vm]$ curl ip.me
   129.114.38.51
   (.venv) [mbs337-vm]$ jupyter lab  # or "jupyter notebook"
   [I 2026-03-01 17:40:03.353 ServerApp] jupyter_lsp | extension was successfully linked.
   [I 2026-03-01 17:40:03.357 ServerApp] jupyter_server_terminals | extension was successfully linked.
   [I 2026-03-01 17:40:03.361 ServerApp] jupyterlab | extension was successfully linked.
   [I 2026-03-01 17:40:03.365 ServerApp] notebook | extension was successfully linked.
   [I 2026-03-01 17:40:03.609 ServerApp] notebook_shim | extension was successfully linked.
   [I 2026-03-01 17:40:03.623 ServerApp] notebook_shim | extension was successfully loaded.
   [I 2026-03-01 17:40:03.625 ServerApp] jupyter_lsp | extension was successfully loaded.
   [I 2026-03-01 17:40:03.626 ServerApp] jupyter_server_terminals | extension was successfully loaded.
   [I 2026-03-01 17:40:03.628 LabApp] JupyterLab extension loaded from /home/ubuntu/mbs-337/.venv/lib/python3.12/site-packages/jupyterlab
   [I 2026-03-01 17:40:03.628 LabApp] JupyterLab application directory is /home/ubuntu/mbs-337/.venv/share/jupyter/lab
   [I 2026-03-01 17:40:03.628 LabApp] Extension Manager is 'pypi'.
   [I 2026-03-01 17:40:03.670 ServerApp] jupyterlab | extension was successfully loaded.
   [I 2026-03-01 17:40:03.673 ServerApp] notebook | extension was successfully loaded.
   [I 2026-03-01 17:40:03.673 ServerApp] Serving notebooks from local directory: /home/ubuntu/mbs-337
   [I 2026-03-01 17:40:03.673 ServerApp] Jupyter Server 2.17.0 is running at:
   [I 2026-03-01 17:40:03.673 ServerApp] http://mbs-337-15:8888/lab
   [I 2026-03-01 17:40:03.673 ServerApp]     http://127.0.0.1:8888/lab
   [I 2026-03-01 17:40:03.674 ServerApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).

Go to a browser and enter the following URL to access the Jupyter Notebook/Lab interface using the public IP
address of your Linux VM (that you got from the ``curl ip.me`` command above):

.. code-block:: text

   http://<your-vm-public-ip>:8888/lab  # or "http://<your-vm-public-ip>:8888/tree" for the classic notebook interface


Additional Resources
--------------------

* `Jupyter Notebook Documentation <https://docs.jupyter.org/en/latest/>`_
* `The Ultimate Beginner's Guide to Jupyter Notebooks <https://medium.com/velotio-perspectives/the-ultimate-beginners-guide-to-jupyter-notebooks-6b00846ed2af>`_
* `Using Jupyter Notebooks - ML for Life Sciences @ TACC <https://life-sciences-ml-at-tacc.readthedocs.io/en/latest/section1/tap_and_jupyter.html#using-jupyter-notebooks>`_
