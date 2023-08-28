# git-vscode-multi-everyting
Demo project for git worktrees, vscode multi-workspace and git repositories on multiple levels

Development activities on multiple git repositories supporting an application suite is a complex situation:
- Multiple Git repository for libraries that are part of the suite
- Git repositories for libraries that the suite developers use in the suite and contribute to
- Major parts of the suite may each have a separate Git repository
- CMake or Ninja may be used to build
- Various build and packaging scenarios apply

Activities that modify the resources stored in Git repositories vary in scope from a 1 line change in a single file of one repository to complex changes to many files spread across multiple repositories, even across projects.  Git provides straightforward tools to manage these activities within a repository with branches and tags.  There is no tool I'm aware of that manages such a change across multiple repositories.

My approach is to create a directory under the suite to organize changes related to an activity.  I can have several activities going simultaneously: a couple of bug-fix branches, changes targeting the last full release, cherry picked from other branches, new feature branches.  These activities can be managed using the relevant git repositories under the Activity folder.  The process is simplified if the copies are registered in git as worktrees. but direct copies of the individual .git repositories work until the various activities start to intermix.


When VSCode introduced multiple root workspaces, I began to think about how most development work on a suite of applications could be managed almost entirely under vscode with its many plugins.  

With this approach, each activity has a VSCode multi-root workspace.  Visual Studio Code is started with the multi-root code-workspace file in each activity folder.  

A hierarchical relationship exists between the Application Suite and the Components that make up the suite.  It would be natural to structure the Application Suite Directory as a container for the components.  But giving a suite its own Git repository and treating it as one of the VSCode multi-root workspaces can be problematic. 

As of August 2023, VSCode may not work as expected if .git files are present on multiple levels of a hierarchy or if one of the roots also contains other folders named as roots. If not careful, you could see more than one copy of the component folders in VSCode's Explorer view.  Moreover, changes made in a file through the suite's Explorer hierarchy would not be recognized by the VSCode Git extension, which views changes via the root identified in the code-workspace file.  

Today, excluding the lower-level roots from a higher-level root is impossible without completely removing them from the explorer. To me, that's a bug in VSCode and I'll report it as such.  

Ideally, you shouldn't have to list folders as roots.  Vscode's multi-root feature was originally designed to include roots outside the current hierarchy.  It was not intended for a single hierarchy containing multiple roots.  

You'll have problems with the VSCode Git Extension If you attempt using a single hierarchy to represent an application suite with multiple Git repositories and use the top-level suite directory for code related to the suite itself, separately from the components. The VSCode Git Extension won't properly handle commits for more than one lower-level repository, and the top-level repo will include all the files in the lower-level repositories. A .gitignore at the root level will also occlude the Explorer's handling of the lower level roots, and not using the .gitignore means they appear twice, ones as the roots, and once as subfolders under the top level.
It's unworkable and I could not find a satisfactory workaround.


Ideally, the presence of a .git at any level of a listed root's hierarchy should be enough to allow proper handling by both the VSCode Explorer and the VSCode Git Extension. The problem currently lies in the Git Extension.  It loses track of the lower level .git repositories and doesn't list changes for all of them. 


So.  Where are we in all this?  We have 
- A Suite folder with materials related to the Application Suite as a whole, possibly with scripts to manage the suite itself
- Activity folders are containers for anything related to that activity, including worktrees with files on specific commits from any number of Git repositories.  Activities may also contain special materials for managing the activity itself
- Component repositories, .git folders, and git worktrees that have to go somewhere.

Addtionally,  
- Suites and Activities may have their own Git Repositories, separate from the repositories of the components.
- It's not a good idea to create multiple git worktrees on the same branch.  While each activity needs to use specific branch worktrees, they should not own their own copies of worktrees on the same branch.

The problem for today is how to structure the folders and place the .git repositories and worktrees so everything works in VSCode.

And what exactly is a Git Worktree?  Originally, Git had just one.  If you wanted to work on two activities simultaneously, you made a copy of the git and checked out a second worktree or alternatively used just one checked-out worktree and did a stash or commit and switched branches.  This approach is challenging when you're working with several repos at once, and they're all on different sets of branches for different activities.  It's especially challenging when you need to switch contexts frequently.

The Application Suite may build several discrete applications, each with a separate repository. Working with multiple git repositories is a given.  Developing an Application Suite might involve several libraries that are part of the suite itself. Outside libraries may be a major part of the application and be subject to patches or merge requests from the Application Suite team as part of an Application Suite activity.  The commits, branches and tags must be managed across the activities and the individual worktrees.  You'll need a map from Activity to a set of key-value pairs (repository-name, (commit, worktree-directory)) where the commit is expressed as a commit id, a branch, or a tag.

The Suite may have a directory structure and code separate from any of the suite's components. So can an activity.

While Visual Studio Code, or any other IDE or Enhanced Code Editor, can always be used on the components one at a time, it's often desirable to work on a set of components as if they were one.  Currently, Visual Studio Code offers the concept of a multi-root workspace, where each root is listed in a code-workspace file.  Each root in the code-workspace file  appears as a separate top-level folder in the VSCode Explorer view. 

Visual Studio's behavior with respect to these folders is less than perfect when any of the multi-root folders is a folder inside the tree of any other. This means that currently, Suites and Activities, if they are to be treated as if they are components, need to be separate directory hierarchies.

We want to support multiple suites of course, so our directory structure gets a bit deeper.

/work holds directories for specific applications
/work/<app-name>  holds component directories for a suite of applications.  Recall that we are going to treat the suite itself and each activity as a component, and that each component will have a Git repository and a set of directories holding the component's source code, data, and documentation.  Most repositories contain more than those three folders, but for demo purposes, we will use just those three in each of our demonstration components.

/work/<app-name>/activities
/work/<app-name>/components
/work/<app-name>/worktrees

These three directories will hold different parts of the puzzle. 

- Activities will contain a .code-workspace file defining a multi-root VSCode workspace referring to a set of worktrees
- Components will contain a Git clone, either the default worktree or a bare clone.
- Worktrees will contain worktrees for each branch of a component used in an activity's vscode multi-root workspace.
- If a specific branch of a component is branch is used in more than one activity, the separate activities will refer to and use the same workspace.

I'm using __ and _ to keep important stuff at the beginning of the components, activities and worktree directories, but this is not required.
