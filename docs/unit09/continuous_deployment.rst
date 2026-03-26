Continuous Deployment
=====================

*Continuous Deployment* is the practice of automatically releasing your code or project to the end
users, provided that it passes all tests. As mentioned, normally it is a good idea to deploy to a 
staging environment (e.g. port 8051) before deploying to production, so that a human in the loop can
do one final check that everything is working as expected. However the key point of this process is
to help ensure the latest components are available to other users in production as quickly as
possible and in an automated way to reduce errors.

After going through this module, students should be able to:

* Set up a GitHub Action for building and pushing a Docker image to the GitHub Container Registry
* Trigger the GitHub Action by pushing a new tag to GitHub
* Use Ansible to deploy a version of the application on a remote server independent of the code base


Build an Image for the GitHub Container Registry
-------------------------------------------------

Rather than commit to GitHub AND push to a container registry like Docker Hub each time you want to
release a new version of code, you can set up an integration between the two services that automates
it. The key benefit is you only have to commit to one place (GitHub), and you can be sure the image
in your container registry will always be in sync.

.. note::

   The content below works similarly on Docker Hub, or the GitHub Container Registry. If setting up
   this integration for Docker Hub, you will need to add a step to inject Docker Hub credentials 
   into the workflow via secrets. For example, see `this link <http://github.com/docker/login-action>`_.


Consider the following workflow, which may be written to ``.github/workflows/push-to-registry.yml``:

.. code-block:: yaml
   :linenos:

   name: Publish Container Image
   
   on:
     push:
       tags:
         - "*"
   
   jobs:
     push-to-registry:
       runs-on: ubuntu-latest
       permissions:
         contents: read
         packages: write
         attestations: write
         id-token: write
   
       steps:
         - name: Check out the repo
           uses: actions/checkout@v4
   
         - name: Log in to the Container registry
           uses: docker/login-action@v4
           with:
             registry: ghcr.io
             username: ${{ github.actor }}
             password: ${{ secrets.GITHUB_TOKEN }}
   
         - name: Extract metadata (tags, labels) for container
           id: meta-data
           uses: docker/metadata-action@v5
           with:
             images: ghcr.io/${{ github.repository }}
   
         - name: Build and push image
           uses: docker/build-push-action@v5
           with:
             context: .
             push: true
             file: ./Dockerfile
             tags: ${{ steps.meta-data.outputs.tags }}
             labels: ${{ steps.meta-data.outputs.labels }}


      

This workflow is triggered when a new tag is pushed (``tags: - '*'``). In contrast to testing on
every push, it makes sense to build and tag containers more selectively, because we would prefer if
tagged containers can be traced back to specific tagged versions of code.

This workflow sets a few permissions near the beginning which are required for building an image. 
As in the previous workflow, this one also runs on an ``ubuntu-latest`` environment.

Then, among the fours steps, it uses the ``docker/login-action`` to log in to GitHub Container
Registry (GHCR) Hub on the command line. The username and password are taken out of the environment.
Certain variables, including ``secrets.GITHUB_TOKEN`` are automatically part of the environment for
every GitHub Action Workflow.

Finally, the workflow uses the ``docker/metadata-action`` to extract tags and the repository name to
assign to the name of the container image, and uses ``docker/build-push-action`` to build and push
the container to the GHCR.


.. tip::

   Don't re-invent the wheel when performing GitHub Actions. There is likely an
   existing action that already does what you're trying to do.


Trigger the Integration
~~~~~~~~~~~~~~~~~~~~~~~

To trigger the build in a real-world scenario, make some changes to your source
code, push your modified code to GitHub and tag the release as ``X.Y.Z`` (whatever
new tag is appropriate) to trigger another automated build:

.. code-block:: console

   [coe332-vm]$ git add *
   [coe332-vm]$ git commit -m "added a new feature to do something"
   [coe332-vm]$ git push
   [coe332-vm]$ git tag -a 0.1.1 -m "release version 0.1.1"
   [coe332-vm]$ git push origin 0.1.1

By default, the git push command does not transfer tags, so we are explicitly
telling git to push the tag we created (0.1.1) to GitHub (origin).

Now, check the online GitHub repo to make sure your change / tag is there, and check the Actions
tab to monitor the status of your build.

.. figure:: images/ghcr_result.png
   :width: 600
   :align: center

   New tag automatically pushed.

If successful, the resulting container images can be found by navigating to your GitHub Profile and
clicking the **Packages** tab near the top center. That image can be pulled using the Docker
commandline interface:


.. code:: console

   [mbs337-vm]$ docker pull ghcr.io/USERNAME/IMAGE:TAG
   # e.g.:
   [mbs337-vm]$ docker pull ghcr.io/wjallen/dash-test:0.1.0

With container images stored in a web-accessible container registry, you can now deploy code and
projects independent of the codebase itself. This is great for arbitrary cloud deployments orchestrated
with tools like Kubernetes or Ansible.


Additional Resources
--------------------

* `GitHub Actions Docs <https://docs.github.com/en/actions>`_
* `Demo Repository <https://github.com/wjallen/api-demo>`_