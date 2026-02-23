Frequently Asked Questions
==========================

Find solutions to common problems below

.. dropdown:: Cannot login to Jetstream VM

   If you try to ssh to mbs-337 and it gives any sort of error or if it asks
   you for a password, it is likely that you removed some necessary lines from
   your ~/.ssh/config on student-login. Contact one of the course instructors
   and we will restore it for you.

.. dropdown:: How do I copy data from my local laptop to mbs-337?

   Assuming you have a file like 'data.csv' on your local laptop and you want to
   copy it to mbs-337. It will require two hops: first copy from laptop to
   student-login, then from student-login to mbs-337:

   .. code-block:: console

      [local]$ ls
      data.csv
      [local]$ scp data.csv username@student-login.tacc.utexas.edu:~/
      Password:
      TACC_Token:

   The file 'data.csv' is now copied to your home directory on student-login,
   so ssh to student-login and from there copy it to mbs-337:

   .. code-block:: console

      [local]$ ssh username@student-login.tacc.utexas.edu
      Password:
      TACC_Token:
      [student-login]$ ls
      data.csv
      [student-login]$ scp data.csv mbs-337:~/
      # no password or token prompt 


.. dropdown:: How do I copy an image from mbs-337 to my local laptop?

   Assuming you have a file like 'output.png' on mbs-337 that you want to copy
   to your local laptop. It will require two hops: first copy from mbs-337
   to student-login, then from student-login to your laptop:

   .. code-block:: console

      [mbs-337]$ pwd
      /path/where/data/is
      [mbs-337]$ ls
      output.png
      [mbs-337]$ logout

      [student-login]$ pwd
      /home/username
      [student-login]$ scp mbs-337:/path/where/data/is/output.png ./

   This will copy 'output.png' from mbs-337 to your home directory on
   student-login. Next, logout of student-login and copy the file to your 
   local laptop:

   .. code-block:: console

      [student-login]$ logout
      [local]$ scp username@student-login.tacc.utexas.edu:~/output.png ./
      Password:
      TACC_Token:



.. dropdown:: ERROR: Corrupted MAC on input.

   If logging in from a windows machine and you encounter the 'Corrupted MAC on
   input.' error, then add the following line to your local laptop ssh config:

   .. code-block:: text
      :emphasize-lines: 6

      Host student-login-jump
          HostName student-login.tacc.utexas.edu
          User your_tacc_username
          IdentityFile ~/.ssh/id_ed25519
          ForwardAgent yes
          MACs hmac-sha2-512


.. dropdown:: I don't know how to format my README.md.

   README.md files use `Markdown <https://www.markdownguide.org/basic-syntax/>`_ syntax, hence the ``.md`` file ending. 
   Markdown is great because it provides a simple way to format text (such as documentation) in an intuitive, organized structure. 
   There's a great general-use example README.md template `here <https://gist.github.com/DomPizzie/7a5ff55ffa9081f2de27c315f5018afc>`_.
   For your homework assignments, you can use the following Markdown template as a guide:

   .. code-block:: markdown

      # Project Title

      Simple overview of use/purpose.

      ## Setup Instructions

      Describe any necessary setup that someone would need to reproduce what you did: E.g.,:

      I ran these exercises in a virtual environment with the **pydantic** and **biopython** packages installed:
      ```bash
      source myenv/bin/activate
      pip3 install pydantic
      pip3 install biopython
      ```

      ## Exercise Descriptions

      ### Exercise 1
      ***Requires pydantic package***
      
      Description of the exercise and what you did. Optionally describe any functions/classes defined within your code:
      1. **find_total_mass**: description
      2. **find_large_proteins**: description

      ## Note on AI Usage (if applicable)

      Describe which/how AI tools were used to complete your homework exercises.

