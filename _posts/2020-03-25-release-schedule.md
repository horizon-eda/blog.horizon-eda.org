---
layout: post
title:  "Release schedule"
date:   2020-03-27 0:0:0 +0100
categories: misc
author: Lukas
---

A few of you were probably wondering what comes after the 1.0
release. Me too.

Since people started asking when the next release will happen and some 
significant features landed since 1.0, I decided to start doing **stable 
releases once a quarter**. To me, this is a good trade off between 
providing access to new features and flakiness due to features having 
seen not enough user testing. Apart from this, a release every three 
months is probably fine for eventual downstream packagers without 
introducing too much churn.

 According to this, you can expect the next
stable version (1.1) to be released on 2020-04-29. Since all of the 
essentials like installer and documentation are out there, the upcoming 
releases will be made purely on a schedule basis, without any 
particular feature goals.

To improve the stability of the releases and differentiate them from 
the regular CI builds, I'll try not to merge big changes in the weeks 
prior to the release.

Usually, only the minor version number will increase (1.0 → 1.1). The 
major number will increase when I see fit.

In case there's a major bug that severely affect day-to-day usage, I'll 
do bugfix release that increments the micro version (e.g. 1.0.1).
