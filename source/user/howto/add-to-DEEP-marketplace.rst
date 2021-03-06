Add you model/service to the DEEP marketplace
=============================================

This document describes how to add your trained service or model to the DEEP marketplace.

Creating the Github repositories
--------------------------------

You model must have a repository to host the code and a repository to host the Dockerfiles.
Both these repositories can he hosted under your personal Github account.
Naming conventions enforce that the Docker repo name is the same as the code repo name with the prefix ``DEEP-OC-``.

A typical example of this can be:

* `deephdc/image-classification-tf <https://github.com/deephdc/image-classification-tf>`_ -
  The Github repo hosting the code of an image classification model.
* `deephdc/DEEP-OC-image-classification-tf <https://github.com/deephdc/DEEP-OC-image-classification-tf>`_ -
  The Github repo hosting the Dockerfiles of the image classification model.

In case you are only developing a service based on an already existing module (like for example developing an animal
classifier based on the image-classification-tf module) you only need to create the Docker repo.

The code repo
^^^^^^^^^^^^^

This is the repo containing the code of your model. If you are adding a service (ie. a trained model) the weights of
the trained model must be stored in a location accessible over a network connection, so that your container can download
them upon creation.

A few MUSTs your code has to comply with in order to ensure compatibility and ease of use:

* your code must be packaged in order to be ``pip`` installable. This should be the default behaviour
  if you used the :doc:`DEEP Data Science template <../overview/cookiecutter-template>` to develop your code.
* your code must be integrated with the DEEPaaS API.
  Check :ref:`this guide <user/overview/api:Integrate your model with the API>` on how to do this.

The Docker repo
^^^^^^^^^^^^^^^

If you used the :doc:`DEEP Data Science template <../overview/cookiecutter-template>` to develop your code, a
template of this repo should have been created alongside the template of your code.

This repo has to contain at least the following files (see the `Generic container <https://github.com/deephdc/DEEP-OC-generic-container>`_
for a template):

* ``Dockerfile``

    This is the file used to build a container from your application. If you developed your application from the
    :doc:`DEEP Data Science template <../overview/cookiecutter-template>` you should have a draft of this file
    (although you might need to add additional code depending on the requirements of your model).

    If you are adding a service derived from an existing module, it is good practice to draw inspiration from the
    Dockerfiles of the module or the services derived from that module (see for example the
    `plant classification Dockerfile <https://github.com/deephdc/DEEP-OC-plants-classification-tf/blob/master/Dockerfile>`_
    derived from the `image classification model <https://github.com/deephdc/DEEP-OC-image-classification-tf>`_).

    Some steps common to all Dockerfiles include cloning the model code repo, pip installing the DEEPaaS API,
    installing rclone and downloading the trained weights if you are adding a service.
    For the details of all these steps please refer to this `Dockerfile example <https://github.com/deephdc/DEEP-OC-image-classification-tf/blob/master/Dockerfile>`_.

* ``Jenkinsfile``

    This is the file that runs the Jenkins pipeline. You can copy this `Jenkinsfile example <https://github.com/deephdc/DEEP-OC-image-classification-tf/blob/master/Jenkinsfile>`_
    replacing the repo names with your own Docker repo name.

* ``metadata.json``

    This file contains the information that is going to be displayed in the Marketplace. You can build your own starting
    from this `metadata.json example <https://github.com/deephdc/DEEP-OC-image-classification-tf/blob/master/metadata.json>`_.
    This metadata will be validated during integration tests when the PR is accepted but you can manually
    `validate the metadata <https://github.com/deephdc/schema4deep>`_  beforehand by running:

    .. code-block:: console

        pip install git+https://github.com/deephdc/schema4apps
        deep-app-schema-validator metadata.json

Making the Pull Request (PR)
----------------------------

Once your repos are set it's time to make a PR to add your model to the marketplace!
For this you have to fork the code of the DEEP catalog repo (`deephdc/deep-oc <https://github.com/deephdc/deep-oc>`_)
and add your Docker repo name at the end of the ``MODULES.yml``.

.. code-block:: console

    git clone https://github.com/[my-github-fork]
    cd [my-github-fork]
    echo '- module: https://github.com/[my-account-name]/DEEP-OC-[my-app-name]' >> MODULES.yml
    git commit -a -m "adding new module to the catalogue"
    git push

You can also make it `online on GitHub <https://github.com/deephdc/deep-oc/edit/master/MODULES.yml>`_.

Once the changes are done, make a PR of your fork to the original repo and wait for approval.
Check the `GitHub Standard Fork & Pull Request Workflow <https://gist.github.com/Chaser324/ce0505fbed06b947d962>`_ in case of doubt.
