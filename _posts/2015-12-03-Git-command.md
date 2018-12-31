---
layout: post
title: "git 命令"
date: 2015-12-03 03:17:00.000000000 +09:00
---

获取远程分支到本地

    git checkout -b newSDK_stable origin/newSDK_stable 

提交本地分支到远程并创建

    git push -u ios_merge   
    git push origin local_branch:remote_branch

这个操作，local_branch必须为你本地存在的分支，remote_branch为远程分支，如果remote_branch不存在则会自动创建分支。

类似，git push origin :remote_branch，local_branch留空的话则是删除远程remote_branch分支。

