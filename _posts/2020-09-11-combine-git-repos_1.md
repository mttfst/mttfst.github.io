---
layout: post
title: Combining two git repositories I  
excerpt_separator: <!--more-->
---
# Introduction
I have two git repositories with similar but not equal content and want to combine them and merge the code. What it makes a bit tricky, in Git's point of view, the repositories are not related to each other.

#### But why am I in this situation?
Almost 2 years ago I started a new project with our institute's code, the atmospheric aerosol and chemistry transport model [cosmo-muscat](https://www.tropos.de/en/research/projects-infrastructures-technology/technology-at-tropos/numerical-modeling/cosmo-muscat){:target="_blank"}.
I planed to do major changes in the dust emission scheme, so I wanted a private copy of the code.
The original code was already stored in a git repository but it was only used to track major versions.
So what I did, I made a copy of the code (not the repository) in a new directory and `git init` a new repository. Retrospectively this wasn't the best idea.

<!--more-->

## My aim
{% include figure.html  href="/assets/combining-git/01-repos.png" title="The current state of both repositories." %}
In the picture you can see the currant state of both repositories on our internal [gitea](https://gitea.io/){:target="_blank"} server.
On the left is (a fork of) the *original* repository, on the right *my copy*. The *original* repository stores the code in the `COSMUS-5.05` directory. In *my copy* I store the code in the directory `cosmo-muscat` and *my repository* additionally contains utilities and a test case. Since is initialised *my copy* as a new repository all commits of the *original* code are not included in *my copy*. So what I need to do is:
- restructure *my copy* so that it matches the *original*
- transfer all commits form *my copy* to the *original* repository
- merging the changes of *my copy* into the *originals* master branch

## Before I start

To make sure I don't mess up with something important, I double-checked that I have backups of everything I will work on. I make fresh clones of both repositories on my local machine and I will only work inside them. So in the worst case, I will be able to restore everything from my "normal" working directories. And also the Remote I use is a copy. I will write in the "real" repository only when I am sure that everything works.


## Preparations
### 1) Set up the right time

For almost two years the repositories diverging and first I need to figure out at which point in history this started.
Therefore I take a look into the `git log`.

I started sometime in 2018 so I searched in the log for all commits after 2018-1-1 and before 2019-1-1 whereby `%C(Yellow)%h` prints a short hash in Yellow, `%C(Green)%cd` the commit date in Green and `%C(reset)%s` the commit message in the default colour.

```
git log --pretty="%C(Yellow)%h %C(Green)%cd %C(reset)%s" --date=local --after="2018-1-1" --before="2019-1-1"  
```

{% include figure.html  href="/assets/combining-git/02-logs.png" title="Git logs of both repositories." %}


The log shows that I stated my side project at October 22, so the latest commit of the *original* that I (should) own in *my copy* is the commit:
```
bb49eae Tue Oct 9 15:40:27 2018
```
So I will start the merging at this point.

### 2) Create the destination

I start in the original repository and create a new branch from the commit I identified above. Then I push this new branch to the remote.

```
git checkout bb49eae
git checkout -b dust
git push -u origin dust
```
As you can see, the new branch 'dust' is now on the gitea server. It is 13 commits behind the  master branch.

{% include figure.html  href="/assets/combining-git/03-new-branch.png" title="The new dust branch." %}

## My merging approaches
To finally combine the repositories I came up with two approaches. Both work well but also have there own very specific disadvantages. You find them in the next Parts of this mini-series.

<!-- #### [Part  II - The Git solution](/combine-git-repos_2/)
#### [Part III - Copy & Paste](/combine-git-repos_3/) -->
