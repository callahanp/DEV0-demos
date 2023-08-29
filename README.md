# DEV0-demos

Demo and test projects for the dev0 development suite.

Development activities on multiple git repositories supporting an application suite is a complex situation:

- Multiple Git repository for libraries that are part of the suite
- Git repositories for libraries that the suite developers use in the suite and contribute to
- Major parts of the suite may each have a separate Git repository
- CMake or Ninja may be used to build
- Various build and packaging scenarios apply

Activities that modify the resources stored in Git repositories vary in scope from 1 line changes in a single file of one repository to complex changes to many files spread across multiple repositories.  Git provides straightforward tools to manage these activities within a repository with branches and tags.  There is no tool I'm aware of that manages such a change across multiple repositories.  You or your team might want to write one, or if the situation isn't too complex, just manage it manually.

My approach is to create an application suite directory containing three folders:

$WORK_DIR/apps/<application-suite-name>/components
$WORK_DIR/apps/<application-suite-name>/worktrees
$WORK_DIR/apps/<application-suite-name>/activities

- Components:  Clones of git forks for the application, for libraries, and for toolchains not formally part of the application
- Worktrees:  Git worktrees representing specific commits, branches, or tags in the clones under components
- Activities:  directories representing a bug-fix, feature branch, release, etc.

The list of activity directories is a list of what you are working on.  cd to one of these directories and start your IDE and you're ready to work.   cd to another activity directory, start the ide there, and you're ready to work in two contexts simultaneously.  Think you can handle that?  Try it with a third!  

setting up an activity folder is easy.  you'll need a list of branches to support the activity, and you may need to create a new branch for any component that you'll be changing.   

for each item in the list of (<component-name>, <branch-name)
```
cd $COMPONENTS_DIR/<component-name>; git worktree add -b <branch_name> $WORKTREES_DIR/<component_name>/branch_name
```
Cries out for a script, doesn't it?  You'll find that script under $COMPONENTS/<toolkit-name>, which has its own repository supported by your team.

One could contemplate configuring an application suite directory with XML or TOML and using a script to set it up as needed and later modify it using the same.

<components>
  <component
    local-name=
    repository_type=
    upstream-url=
    origin-url=
    >
  </component>
  <activity
    name=
    <component
      name=
      branch=>
  </activity>


  Where would you put such a thing?  How about the application suite [DEV0](https://github.com/callahanp/DEV0)
