deployer-loader
===============
|

.. image:: http://img.shields.io/packagist/v/sourcebroker/deployer-loader.svg?style=flat
   :target: https://packagist.org/packages/sourcebroker/deployer-loader

.. image:: https://img.shields.io/badge/license-MIT-blue.svg?style=flat
   :target: https://packagist.org/packages/sourcebroker/deployer-loader

|

.. contents:: :local:


What does it do?
----------------

This package allows to:

1. Register your project vendor classes to be used in deploy.php. Read "Include class loader" for more info why you
   should not include your project vendor/autoload.php in deploy.php.
2. Load single task/setting file.
3. Load multiple tasks/settings files from folders.


Installation
------------
::

  composer require digitalerase/deployer-loader


Usage
-----

Include class loader
++++++++++++++++++++

If Deployer is used as phar from global install or from local in ``./vendor/bin/dep`` (installed from ``deployer/dist``) then
it is already including his own ``vendor/autoload.php``. If in ``deploy.php`` file we will require ``vendor/autoload.php``
from our project then it's like asking for trouble because we are joining two autoloads with non synchronized dependencies.
The second composer ``vendor/autoload.php`` added by us in ``deploy.php`` has priority because the composer uses the
``prepend`` parameter of ``spl_autoload_register()`` method which adds an autoloader on the beginning of the autoload
queue instead of appending it. So classes from our project will be used before classes from Deployer phar.

The solution is to include in ``deploy.php`` the autoload.php from ``digitalerase/deployer-loader``.

Using ``spl_autoload_register()`` it will register new closure function to find classes and it will register itself without
``prepend`` parameter. So first classes from Deployer phar autoload will be used and if they will not exists
there will be fallback to classes from the main project vendors.

How to use it? Just include autoload at the beginning of your ``deploy.php`` (and remove ``vendor/autoload.php`` if you had one)

::

  require_once(__DIR__ . '/vendor/digitalerase/deployer-loader/autoload.php');


After this point in code you can use all vendor classes declared in psr4 of your ``composer.json`` files.


Loading deployer tasks
++++++++++++++++++++++

The package digitalerase/deployer-loader allows you also to include single files or bunch of files from folder
(recursively).

- Example for loading single file:

  ::

   new \SourceBroker\DeployerLoader\Load(
      [path => 'vendor/digitalerase/deployer-extended-database/deployer/db/task/db:copy.php'],
      [path => 'vendor/digitalerase/deployer-extended-database/deployer/db/task/db:move.php'],
   );

- Example for loading all files from folder recursively:

  ::

   new \SourceBroker\DeployerLoader\Load(
      [
        path => 'vendor/digitalerase/deployer-extended-database/deployer/db/'
        excludePattern => '/move/'
      ],
      [
        path => 'vendor/digitalerase/deployer-extended-media/deployer/media/'
      ],
   );

  You can use preg_match "excludePattern" to exclude files.


Changelog
---------

See https://github.com/digitalerase/deployer-loader/blob/master/CHANGELOG.rst
