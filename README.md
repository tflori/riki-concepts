# Conceptual ideas for ríki community

## Project

Riki is a project to bring developers the idea near to design their own project structure and get a clear understanding
of what is reusable code and what not. The framework itself contains only a few classes and has no tight coupling to
other libraries that are recommended.

There will be a skeleton project without any examples but in the guide it is also recommended to start from an empty
project. The skeleton project has some additional dependencies that are only recommended. 

## Goals of the community website

- example website for this framework
- documentation for this framework
- community base for this framework
 
## Target group definition

The main target group for this website are developers using this framework or plan to use this framework in the future.

A second target group could be project managers looking for advantages of this framework and websites that are using the
framework.

## Categories

- Blog: A blog with posts what is new in this framework and other things related to development with this framework.
- Guide: A book that describes how to start with a new project using this framework
- Documentation: The documentation of the framework in the latest stable version (somehow synced)
- Exchange: A forum similar to the concept of stack exchange
- Contribute: A guide how to contribute to this framework (static page)

### Homepage

- Shows a brief description about the framework
- A big primary colored button "how to get started" (guide)
- A list of teasers to the latest blog posts

Detailed concept: [home](home)

### Search

An important feature of the community website is the search that searches overall categories and is always available.

Detailed concept: [search](search)

### Blog

- Blog posts can be written by contributors and users with high reputation (revised by a contributor)
- A Blog post can be up and down voted what brings reputation for the author
- A Blog post can get comments that also can be voted up and down
- Blog posts and comments are written in MarkDown and follow the same rules as github MarkDown
- The teaser is generated automatically but can be defined by the author as well

Detailed concept: [blog](blog)

### Guide

- The guide is not only about using the framework
- The guide is structured as follows
  - to be continued...
- Each guide page can have comments with up and down voting
- The guide is written in github MarkDown as well
- The guide can be printed as a complete book
- The guide can be downloaded as epub

Detailed concept: [guide](guide)

### Documentation

- The documentation gets synchronized with the framework repository every time a new tag is pushed
- No comments here

Detailed concept: [docs](docs)

### Exchange

- Like stack exchange up and down voting questions and answers but with github MarkDown
- Live search while writing questions to avoid duplicates

Detailed concept: [exchange](exchange)

## UX concept

### Login

On the upper right side of the website there is always either a login or the information who is logged in and what he
got new (comments on answers/questions, reputation changes, direct messages).

### Header

The header is large and shows the large logo of the framework. After some seconds it scrolls down to so that only the
fixed navbar is shown.

The header has an animation that illustrates what is riki and where the slogan comes from **Connecting Libraries**. The
animation is triggered by scrolling.

## Framework

The framework itself has no fixed directory structure instead it provides only some classes that you otherwise have to
rewrite for every project. These classes should not contain high sophisticated logic to make them configurable but have
to be extendable and therefore reusable.

A detailed [concept of the framework](framework) is available. 

## Skeleton

There will be a skeleton that can be used as a project template for generating an application using the framework. This
skeleton should have the ability to be as minimal as possible while also options exist for fully implemented libraries
for http routing, io handling on console or scheduling asynchronous jobs.

A detailed [concept of the skeleton](skeleton) is available. 

## Technical concept of the community page

The technical concept is the main idea of the riki philosophy and you will find a lot of details that reappear in the
guide.

The application is using [PSR-4](https://www.php-fig.org/psr/psr-4/) for auto loading classes with the following
namespaces:

 - namespace `App\` folder `app/`
 - namespace `Community\` folder `src/`
 - namespace `Test\` folder `tests/`
 
As coding style guide the application is using [PSR-2](https://www.php-fig.org/psr/psr-2/).
 
### Testing

All code should be tested in this application. We plan to work test driven. Code that can not be tested should be
marked with ignore code coverage annotations to remain a 100% coverage to see when something is not tested.

Tests are divided into:

 - unit tests that don't require any external dependency such as a database, a web service or similar
 - integration tests that may require a database, a cache engine or a key value storage (e. g. redis)
 - end to end tests that require the application to run including all dependent services
 
All stages have to be executable in the local environment and on the ci environment (travis ci).

### Development environment

The development environment is using docker to maintain equal versions on each developer machine. In the repository
the application provides a docker-compose example that has to be adjusted for each developer.

The local environment has to allow debugging using xdebug.

### Dependencies

The application requires a postgres database, a redis database and php >= 7.1. The detailed dependencies for php,
php-modules and php-libraries is defined in the `composer.json` and is the only source of truth.
