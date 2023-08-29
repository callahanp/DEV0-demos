# Demonstration projects using [DEV0](https://github.com/callahanp/DEV0)

Demo project for the DEV0 toolchain

DEV0 Retrieve, Build, Run, and Maintain Software

## Demos

Basic - Basic is not buildable, Basic is kept as simple as possible to demonstrate how worktrees are created for a set of git repositories.

## Demo Location

This project sets up demonstration application suites under a folder designated with an environment variable: DEV0_DEMO_APPS

A typical dev0 application suite directory contains three sub-directories of

## Demo Construction

A script will create a complete demo application

## Application Suites - Components, Activities & Worktrees

$DEV0_DEMO_APPS/<app-name>

$DEV0_DEMO_APPS/<app-name>/components
$DEV0_DEMO_APPS/<app-name>/activities
$DEV0_DEMO_APPS/<app-name>/worktrees

## Components

- Clones of all repositories needed in the development of the application suite.
- Clones of any tool libraries not part of the application
- Clone of a project that builds and maintains the dev0 support for the application
- Clones of anything else needed to support development activities

## Activities

- An activity corresponds to a specific change to the application usually involving branches, tags or commits on each of the components that make up the application
- The dev0 support component of the application provides a configuration file for the activities.  Using this configuration file, dev0 provides a way to initially populate the entire application development tree for a group of activities.
- Separate configuration files for just an activity are allowed.

## Worktrees

- Worktrees are created for a component based on the branch, tag, or commit required for a given activity.

Building the app will create additional Application Suite directories

$DEV0_DEMO_APPS/<app-name>/build
$DEV0_DEMO_APPS/<app-name>/install
$DEV0_DEMO_APPS/<app-name>/web-root
$DEV0_DEMO_APPS/<app-name>/package
