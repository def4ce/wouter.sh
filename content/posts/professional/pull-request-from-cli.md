+++
title = "Open a pull-request to GitHub from the CLI"
date = "2019-07-16"
category = "Git"
tags = ["git", "cli", "os x"]
+++

After spending some time getting [git pull-request](https://www.git-scm.com/docs/git-request-pull) to work I decided it was to much of a hassle and would try to make my own function.  

If you are on `OS X`, drop the following in your `.bashrc` or your preferred `$SHELL` equivalent and source it:

```bash
function git_pull_request() {
      REPOURL=`git config --get remote.origin.url`
      REPO=`basename -s .git $REPOURL`

      BRANCH=`git symbolic-ref -q --short HEAD`

      GIT_USER=`git config user.name` 

      if [[ -z $GIT_USER ]]
          then
              echo "Please set your Git user name by running: git config --global user.name '\$USERNAME'"
              exit 1
      fi

      PR_URL="https://github.com/${GIT_USER}/${REPO}/pull/new/${BRANCH}"

      open $PR_URL
}
```

Now you will have access to the `git_pull_request` command. After you pushed your changes you can use this command from anywhere in the repository and it will open your default browser with a new GitHub pull-request.