---
title: How to create github PR
date: 2019-09-23
---

As more and more open source codes start to use github to maintain their code
and use pull requests to accept new patches. In this artical I will introduce
how to create a github PR(Pull Requests).

For the detils of github PR, please see [this page](https://help.github.com/en/articles/about-pull-requests).


1. You need to have a github account :)
1. Open the project you want to contribute. I will use [CKI-project](https://github.com/CKI-project/tests-beaker) as my example
1. Click "Fork" on the right top of the page.
1. Then github will create a same project under your account, with the
   description of `forked from CKI-project/tests-beaker`
1. Now clone the project to you localhost. In my example the addr is
   `https://github.com/liuhangbin/tests-beaker.git`, in your side you need to
   change the `liuhangbin` to your own accound id.
1. On local host, create a new branch, e.g. "network_fix", via
   `git check -b network_fix`. Add your fixes in this branch
1. After adding fixes or update with your patch, push the changes to upstream via
   `git push --set-upstream origin network_fix`
1. Now there will has a notification like: Create a pull request for 'network_fix' on GitHub by visiting:
   `https://github.com/liuhangbin/tests-beaker/pull/new/network_fix`. Or you
   just open your fork page on github, it will show a `Compare & pull
   request` button.
1. Open the URL or click `Compare & pull reuest`, you will in the page of `Open a pull request`.
   If it's only one commit, then every thing should be done and just click
   `Create pull request`. Now you have create a [PR(Pull Request)](https://github.com/CKI-project/tests-beaker/pull/378) to upstream
   project. If it's a patch set, you need to fill the patch subject and
   comments separately.



### How to update your fork to upstream version

Just replace your repo addr to upstream repo addr in your .git/config, and
git pull the master branch directly. Then comment the upstream repo addr and
push the latest code to your repo. This will update your code to upstream
version. Another way is like [this page](https://github.com/KirstieJane/STEMMRoleModels/wiki/Syncing-your-fork-to-the-original-repository-via-the-browser)
said, but I didn't try it succeed.
