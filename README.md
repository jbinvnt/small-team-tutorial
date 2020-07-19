# GitHub Small Team Workflow

Originally created for the [LMVP project](https://github.com/dsc-umass/lmvp). This guide is also easily adaptable for any GitHub project with a small team. Just replace the project names, Git URLs, and the reviewer username.

A bonus guide at the end explains how to run a Docker Compose project.

## Contributing and Workflow

Welcome to the team! The work for this project is managed using GitHub issues. Each issue represents something that needs to be done: usually fixing a bug or adding a new feature. **Every change you make** should have its own **issue** and its own **branch**. If you've used Jira before, you will recognize issues as being the analogous to tickets. First, clone the repository by running:

```
$ git clone git@github.com:dsc-umass/lmvp.git
$ cd lmvp/
```

### Create a new issue

Go to the *Issues* tab at the top of GitHub, then click the green *New issue* button. Give it a short descriptive title and add more information in the text box below. Then make sure you choose the **gear next to "Projects"** and click on LMVP.

![Assign GitHub project](/docs/images/AssignGitHubProject.PNG?raw=true)

You can assign an issue to yourself or a teammate right away if you've discussed it and are ready to start work. Alternatively, you can leave it unassigned and someone can later take responsibility for it when they need a new task.

### Claim an issue

Go to the *Projects* tab at the top, and choose LMVP. You should see a page with several columns, each containing cards showing issues. Drag the issue you're about to start from the "New" column to "Working on". Make sure that the issue is assigned to you if it isn't already (but don't work on an issue assigned to someone else or change its assignment without asking them first).

Almost always you should only work on one issue at a time. This is to make sure that features get finished and the team makes steady progress. There are some exceptions such as fixing a significant bug and "unblocking" someone by fulfilling a prerequisite for their own work.

### Create a branch

This is one of the most important steps in the contribution workflow. Especially if you're new to Git, it's easy to accidentally make changes on the wrong branch and **erase your work or someone else's**.

Each branch needs to be named after the issue that you're working on. In GitHub, each issue has a number as shown here:

![Assign GitHub project](/docs/images/GitHubIssueNumber.PNG?raw=true)

You should always name your branch `LMVP-<your issue number here>`. So in my example the branch I create should be named `LMVP-18`. Here are the commands I would run to create this branch:

```
$ git checkout master
$ git checkout -b LMVP-18
```

Remember to replace `18` with your own issue number.

Now, make your changes to the code. To save a benchmark of your work at any time, run:

```
$ git add .
$ git commit -m "Put a message describing your work here"
```

Your commit message should be short: the standard is about 50 characters. Also, it should be written in the [imperative tense](https://chris.beams.io/posts/git-commit/#imperative).

If you need to write a longer message, use:

```
$ git commit -m "Add title here" -m "Your full, longer description can go here. Still, don't write an essay."
```

### Be careful with pushing!

Pushing a branch allows you to send your code to GitHub so others can see your work and it can be saved online. But pushing incorrectly can be **dangerous**. Always make sure you are pushing to the **correct branch** and never push with the `--force` flag, except after a rebase.

Before pushing, always make sure you are on the correct branch:

```
$ git status
On branch LMVP-18
nothing to commit, working tree clean
```

Never push to the `master` branch. To get to a different branch, run

```
$ git checkout <correct branch name here>
```

If the branch doesn't exist, see the above section for how to create it. Once you're on the right branch, run:

```
$ git push -u origin LMVP-<your issue number here>
```

You can make and push multiple commits. Any subsequent times you push commits while on this branch, you can just run `git push` without the extra arguments.

### Creating a pull request

A pull request is used to indicate that you're ready for your work to be reviewed before being added to the master version of the codebase. Pull requests provide a place for others to comment with feedback and for you to make changes in response to their suggestions. If you've used GitLab before, you will recognize pull requests as being analogous to merge requests.

Go to the *Pull requests* tab at the top of GitHub, then click the green *New pull request* button. Make sure that **base** is set to master and change **compare** to be the branch with your changes. Remember to [link the pull request to your issue](https://docs.github.com/en/github/managing-your-work-on-github/linking-a-pull-request-to-an-issue).

Also set the reviewer in the upper right. In most cases this will include me, **jbinvnt**, and other teammates who can offer feedback. After that, you can submit the pull request. Do not merge your own pull request without review!

### Reviewing a pull request

In response to review, you can make new commits on your branch and push them. They will be visible in the pull request once they are pushed. Make sure to notify a reviewer when your new commits respond to their feedback.

### Merging a pull request

Again, **do not** merge a pull request until it has been reviewed by the reviewers. Choose the dropdown arrow to make sure the merge button says *Squash and merge*. This will keep things organized by creating one commit in master to encompass all the commits in the pull request.

The repository is set up to delete the branch of the pull request once it has been merged. This keeps things organized on the server side, and the creator of the branch still has a copy on their local machine.

### Rebasing

You will have a merge conflict if someone else merges their pull request into master after you have created your branch. To resolve this, run:

```
$ git checkout master
$ git pull
$ git checkout <your branch name here>
$ git rebase -i master
```

This will likely open a file in a text editor. In most cases you should not edit this file. Just type `:wq` and then `ENTER` for Vim, or `CTRL + O` and then `CTRL + X` if you get Nano. If there is a conflict, look for files with:

```
<<<<<<< HEAD: ...
...
=======
...
>>>>>>> ...
```

These show incoming changes that were added to master since you created your branch. Remove these markers and replace them with the code you want in the final version of your branch, while being careful not to delete anything that someone else added and should stay in your version. Finally, run:

```
$ git add .
$ git rebase --continue
```

And repeat for any other conflicts in the rebase. Once you're done, it's now time for the only scenario in which you should use the `--force` flag. First, run

```
$ git status
```

to make sure that you really are on the correct branch. It is normal to see a message saying that your branch has diverged.

Now, you can run

```
$ git push --force
```

and your rebased branch should be ready to merge once it is reviewed.

## Running the project

Running the project is best done with Docker Compose. Learn how to install Compose [here](https://docs.docker.com/compose/install/).

First, go to the repository directory. Then run:

```
$ export DB_PASSWORD=arbitrarypassword
$ docker-compose run web python3 manage.py migrate
$ docker-compose up
```

If you get an error connecting to the database, you may just need to restart the containers. Do this with

```
$ docker-compose stop
$ docker-compose up
```

Now the container should be running! You can visit the address in a web browser or [make API requests with cURL](https://linuxize.com/post/curl-rest-api/) to start.

### Testing and other commands

When you want to run the tests, use `docker-compose -f docker-compose.test.yml up`. To run an arbitrary command in a specific service, the pattern is `docker-compose run <servicename> <commandname>`. To get to a shell you can type `bash` as the command name. After changes to the repository, you should rebuild the container with `docker-compose build`.
