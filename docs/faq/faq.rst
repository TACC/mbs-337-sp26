Frequently Asked Questions
=========================

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

