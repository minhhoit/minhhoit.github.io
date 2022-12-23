---
layout: post
title: Git Config is useful for developer
comments: false
categories: ["tips"]
tags: ["git", "tips"]
---

To use Git effectively, there are many useful Git config tops for developers. In the process of working, I have encountered some confusion in configuring and using git, and also share it here for your reference.

# Line Ending Character
>Line Ending Character is a character representing the end of a line in a file, usually use enter to add this character and start a new line.

Open the terminal and type the following 2 lines in the project to configure it for that project alone:
```
git config core.autocrlf false
git config core.eol input
```

## Explain
1. `git config core.autocrlf false`: Config does not automatically convert newline characters according to the operating system in use (Windows users often have this problem).
2. `git config core.eol input`: Config preserves the newline character of the file when pulling from the remote.

# File permission

>Git also has a config track about changing file permissions, and tracking this change according to me is also unnecessary and confusing as above, it is best to turn it off for ease.

Continue to open terminal and type:
```
git config core.fileMode false
```

Or config global
```
git config --global core.fileMode false
```

Hope the article will be useful for you in your work.