+++
draft = true
title = "Generate .gitignore from the command line"
date = "2019-07-16"
category = "Git"
tags = ["git", "cli"]
+++

You can generate .gitignore files from the command line by using the api of [gitignore.io](https://gitignore.io/).

To do this you need to create a git [alias](https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases).

```bash
git config --global alias.ignore \
'!gi() { curl -sL https://www.gitignore.io/api/$@ ;}; gi'
```

When you created the alias you have access to the `git ignore` command. If you run `git ignore list` you will get a list of supported languages, tools, os's and applications.

For example, running `git ignore python,visualstudiocode,osx` will dump a list of ignored files for Python, VSCode and OS X to `stdout`.

If you want to create the `.gitignore` file you need to redirect the `git ignore` command.

Taking the previous example, if you run command again but add redirection like this: `git ignore python,visualstudiocode,osx > .gitgnore`, you will create a new .gitignore file. If you want to append to an existing file make sure you change the single `>` to `>>`.
