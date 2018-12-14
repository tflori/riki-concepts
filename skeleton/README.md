# Concept for ríki skeleton

There will be a skeleton that can be used as a project template for generating an application using the framework. This
skeleton should have the ability to be as minimal as possible while also options exist for fully implemented libraries
for http routing, io handling on console or scheduling asynchronous jobs.

## How it works

Instead of having a lot of example files that have to be deleted we have only a installation routine where you can 
choose which files to install.

The features are separated and in the first version we will have the following features:

- misc - The basic files required
- docker - Docker files for local environment and/or building docker images
- console - Advanced setup for writing command line applications using ulrichsg/getopt-php and tflori/hugga
- routing - Advanced setup for writing http services using nikic/fast-route and tflori/tal

Each feature can extend or overwrite existing files.

## Usage

`riki-skeleton` can also be installed globally on your system. This way you can directly start the installation
routine for a new project. We recommend to use this option - example:

```console
$ composer global require tflori/riki-skeleton
...
Generating autoload files
$ riki-skeleton create-project projects/my-new-project
...
$ cd projects/my-new-project
$ composer install
```

## Future

We plan to add more features to add and an additional mode for global installations to add features to an existing
project. Also dependencies could be possible (for example an feature API that requires routing to be available).
