# HowGitWorks

> I init a local repo, and copy the `.git` directory to this `HowGitWorks` repo.
> Then, I start to do some simple git commands, but when ever I command, I replace the .git in the `HowGitWorks` repo.
> So that I can study the changes in `.git` directory, I especially focus on the `.git/objects/` directory and check the object content

- git cat-file
  - use this command to check the binary file under `.git/objects`
  - `git cat-file -p abcd###`: check the content of `.git/objects/ab/cd###`
  - `git cat-file -t abcd###`: check the type of `.git/objects/ab/cd###`

[List all binary files under .git objects](https://github.com/ChaoLiou/HowGitWorks/wiki/List-all-binary-files-under-dotgit-objects-)
