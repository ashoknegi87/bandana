.. include:: ../../common/global.rst

Dependency management with Composer
===================================

The primary method for managing dependencies with your Acquia BLT-based
projects is through the use of `Composer <https://getcomposer.org/>`__.

.. _blt-composer-usage-overview:

Composer usage overview
-----------------------

`Composer <https://getcomposer.org/>`__ should be used to manage Drupal core,
all contributed dependencies, and most third party libraries. The primary
exception to this is front-end libraries, which can be managed using a
front-end specific dependency manager, such as `Bower <http://bower.io/>`__ or
`NPM <https://www.npmjs.com/>`__.

`Why do we use Composer
<http://blog.nelm.io/2011/12/composer-part-1-what-why/>`__ for
dependency management? It is the dependency manager used by Drupal core.

Be sure to familiarize yourself with Composer's `basic usage
<https://getcomposer.org/doc/01-basic-usage.md>`__, especially how the `lock
file
<https://getcomposer.org/doc/01-basic-usage.md#composer-lock-the-lock-file>`__
is used. You should commit *both* the ``composer.json`` and
``composer.lock`` files to your project, and every time you update the
``composer.json`` file , you must also run the following command to update the
``composer.lock`` file:

.. code-block:: bash

     composer update

Never manually edit the ``composer.lock`` file.

Notable Composer concepts
~~~~~~~~~~~~~~~~~~~~~~~~~

-  Why `dependencies should not be committed
   <https://getcomposer.org/doc/faqs/should-i-commit-the-dependencies-in-my-vendor-directory.md>`__

-  The role of `composer.lock
   <https://getcomposer.org/doc/01-basic-usage.md#composer-lock-the-lock-file>`__

-  How to use `version constraints
   <https://getcomposer.org/doc/articles/versions.md>`__

   -  `Why using unbound version constraints is a bad idea
      <https://getcomposer.org/doc/faqs/why-are-unbound-version-constraints-a-bad-idea.md>`__

-  `The difference
   <http://stackoverflow.com/questions/16679589/whats-the-difference-between-require-and-require-dev>`__
   between ``require`` and ``require-dev``

Recommended tools and configuration
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

-  Globally install `pretissimo <https://github.com/hirak/prestissimo>`__
   for parallelized composer downloads by running the following command:

   .. code-block:: bash

        composer global require "hirak/prestissimo:^0.3"

-  If you have `Xdebug <https://xdebug.org/>`__ enabled for your PHP CLI
   binary, to dramatically improve performance it is highly recommended that
   you disable Xdebug.

Contributed projects and third-party libraries
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

You can find all contributed projects hosted on Drupal.org (including Drupal
core, profiles, modules, and themes) on Drupal `Packagist
<https://packagist.org/packages/drupal/core>`__, a Drupal.org-hosted
packagist server. You must specify this URL in your ``composer.json`` file by
adding the following code to allow Composer to discover the packages:

.. code-block:: text

     {
        "repositories": {
            "drupal": {
                "type": "composer",
                "url": "https://packages.drupal.org/8"
               }
           }
      }

Most non-Drupal libraries can be found on `Packagist
<http://packagist.com/>`__.

For any required packaged not hosted by the preceding websites, you can define
your own array of `custom repositories
<https://getcomposer.org/doc/05-repositories.md#repository>`__ for Composer to
search.

.. note::

   Composer versioning is not identical to Drupal.org versioning.

Composer resources
~~~~~~~~~~~~~~~~~~

-  `Composer Versions <https://getcomposer.org/doc/articles/versions.md>`__
-  `Using Composer to Manage Drupal Site Dependencies
   <https://www.drupal.org/docs/develop/using-composer/using-composer-to-manage-drupal-site-dependencies>`__
-  `Drupal Composer package naming conventions
   <https://www.drupal.org/node/2471927>`__
-  `Packagist <http://packagist.com/>`__: Find non-drupal libraries and their
   current versions.


.. _blt-install-dependencies:

Installing dependencies
-----------------------

To install a new package to your project, use the ``composer require``
command. This command adds the new dependency to your ``composer.json`` and
``composer.lock`` files, and downloads the package locally. For example, to
download a module, run the following command, replacing ``[module]`` with the
module you want to download:

.. code-block:: bash

     composer require drupal/[module]

After you run the command, be sure to commit your ``composer.json`` and
``composer.lock`` files.


.. _blt-update-dependencies:

Updating dependencies (core, profile, module, theme, libraries)
---------------------------------------------------------------

To update a single package, run the ``composer update [vendor/package]``
command, replacing ``[module]`` with the module you want to update:

.. code-block:: text

     composer update drupal/[module] --with-dependencies

To update all packages, run the following command:

.. code-block:: text

     composer update

After you run the command, be sure to commit your ``composer.json`` and
``composer.lock`` files.


.. _blt-remove-dependencies:

Removing dependencies
---------------------

To remove a package from your project, use the ``composer remove``
command, replacing ``[module]`` with the module you want to remove:

.. code-block:: text

     composer remove drupal/[module]

After you run the command, be sure to commit your ``composer.json`` and
``composer.lock`` files.


.. _blt-patch-project:

Patching a project
------------------

.. tabs::

   .. tab:: 10.x

      For information about patch naming, patch application, patch ignoring,
      and patch contribution guidance, see `patches
      <https://github.com/acquia/blt/blob/10.x/docs/patches.md>`__.

   .. tab:: 9.2.x

      **Modifying Acquia BLT's default Composer values**

      Acquia BLT merges default values for ``composer.json`` using
      `wikimedia/composer-merge-plugin
      <https://github.com/wikimedia/composer-merge-plugin>`__:

      .. code-block:: text

           "merge-plugin": {
           "require": [
              "blt/composer.required.json",
              "blt/composer.suggested.json"
           ],
           "merge-extra": true,
           "merge-extra-deep": true,
           "merge-scripts": true,
           "replace": false,
           "ignore-duplicates": true
           },

      This merges the require, require-dev, autoload, autoload-dev, scripts,
      and extra keys from BLT's own vendored files. The merged values are
      split into the following groups:

      #. ``composer.require.json`` ??? These packages are required for Acquia
         BLT to function properly. You can change their versions, but do not
         remove them.

      #. ``composer.suggested.json`` ??? You can remove the suggested packages
         by deleting the ``blt/composer.suggested.json`` line from your
         ``composer.json`` file.

      If you want to override the default version constraint for a package
      provided by Acquia BLT, you can define the desired version in your root
      ``composer.json`` file. For this to work, your configuration for the
      merge plugin must include the following items:

      .. code-block:: text

         "replace": false,
         "ignore-duplicates": true

      **Merging additional composer.json files**

      In situations where you have local projects that have their own
      ``composer.json`` files (such as a custom module), you can merge them in
      by including the ``composer-merge-plugin``. Reference these additional
      ``composer.json`` files in the extra section of your root
      ``composer.json`` file:

      .. code-block:: text

         "extra": {
           "merge-plugin": {
             "require": [
               "docroot/modules/custom/example/composer.json"
           ]
         }
       }


.. _blt-front-end-dependencies:

Front-end dependencies
----------------------

Drupal 8 does not have a definitive solution for downloading front-end
dependencies. Acquia suggests you refer to the following solutions:

-  Load the library as an external library. See `Adding stylesheets (CSS)
   and JavaScript (JS) to a Drupal 8 module
   <https://www.drupal.org/developing/api/8/assets>`__.

-  Use a front-end package manager (such as `NPM <https://www.npmjs.com/>`__)
   to download your dependencies. Then, use Acquia BLT's
   ``source:build:frontend-assets`` target-hook to trigger building those
   dependencies (such as call ``npm install``) in your theme directory using
   these hooks. For more information, see :doc:`/blt/developer/frontend/`.

-  Commit the library to the repository, typically in
   ``docroot/librares``.

-  Add the library to your ``composer.json`` file by using a `custom
   repository <https://getcomposer.org/doc/05-repositories.md>`__.
   Designate the package as a ``drupal-library`` and define an
   ``installer-paths`` path for that package type to ensure that it is
   installed to ``docroot/libraries.`` Ensure that it can be discovered
   in that location. See `example composer.json
   <https://gist.github.com/mortenson/a5390d99013b5b8c0254081e89bb4d47>`__.

Contributed projects should provide the ability to download and discover
the libraries. If you are using a contributed project, it is suggested
that you patch the project to support one of these strategies.

If you cannot, commit the dependency. You can use a custom ``.gitignore``
file for your project.

Ensure the dependency is copied to the deployment artifact, and supply
your own, custom ``.gitignore`` file to use in the deployment artifact.

For more information, see :doc:`/blt/extending-blt/`.

.. Next review date 20200425
