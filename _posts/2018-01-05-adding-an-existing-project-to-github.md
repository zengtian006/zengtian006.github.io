---
title: Adding an existing project to GitHub
layout: post
date: '2018-01-05 04:12:00'
tags: github
card-image: https://banner2.kisspng.com/20180824/jtl/kisspng-computer-icons-logo-portable-network-graphics-clip-icons-for-free-iconza-circle-social-5b7fe46b0bac53.1999041115351082030478.jpg
post-card-type: image
---

1. [Create a new repository](https://help.github.com/articles/creating-a-new-repository/) on GitHub. To avoid errors, do not initialize the new repository with README, license, or gitignore files. You can add these files after your project has been pushed to GitHub.

2. Initialize the local directory as a Git repository.
`git init`

3. Add the files in your new local repository. This stages them for the first commit.
`git add .`

4. Commit the files
`git commit -m 'First commit'`

5. Add remote repository URL
```
# Sets the new remote
git remote add origin remote repository URL
# Verifies the new remote URL
git remote -v
```

6. Push the changes to GitHub
`git push -u origin master`