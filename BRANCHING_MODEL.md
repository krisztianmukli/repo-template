🇬🇧 [en](BRANCHING_MODEL.md)&nbsp;&nbsp;|&nbsp;&nbsp;🇭🇺 
[hu](BRANCHING_MODEL.hu.md)

# Simplified Git-flow

```
                        RELEASE TAG
o----------------------------o-----------------o------------o------> MAIN
 \                          /  \                \----------/ HOTFIX
  \                        /    \                          \
   \----------------------/      \--------------------o-----o------> DEVELOP
                                  \                  /
                                   \----------------/ FEATURE

```

- `main`: should contain the most stable, production ready code. In other words, 
we can pull from master and build and deploy at anytime.
- `develop`: is where most of the development happen. After code in `develop` 
stable enough, it can be merged back to `main`. Every release should be tagged 
(Ex: version 2.1.3). 
- `feature`: is where each feature development happens. Should be only branched 
from `develop` and commited back to `develop`. After fully merged to `develop`, 
individual `feature` branch can be deleted.
-  `hotfix`: is where we do bug fix in current release. After finish fixing the 
bug, `hotfix` get merged to both `main` and `develop`, then it can be deleted.

## Optional `release` Branch

This branching model is primarily intended for small development teams (1–5 
members). When the `develop` branch reaches a state where more extensive 
testing, bug fixing, or a full QA cycle is required, it can become challenging 
for the team to continue development without disrupting the testing process with 
new feature merges.

In such cases, there are two possible approaches:

1. **FEATURE FREEZE on the `develop` branch**  
   The `develop` branch does not accept new feature merges until the release is
   completed.

2. **Creating a `release` branch from `develop`**  
   Finalization, bug fixing, and QA activities for the upcoming release take
   place in the `release` branch. Meanwhile, the `develop` branch may continue
   to receive new feature merges, allowing development work to progress without
   being blocked by the stabilization process.

   Any modifications made in the `release` branch must be merged back into
   `develop`. Additionally, all `feature` branches created *before* the
   `release` branch was introduced must be updated with the changes from the
   `release` branch to prevent regressions (i.e., previously fixed issues
   reappearing).

**Unless otherwise specified, we always use the FEATURE FREEZE approach.**

#### Naming convention:

- `feature`: prefix with `feature/` and then a short descriptive name  
  *ex:* `feature/new_mission`
- `hotfix`: prefix with `hotfix/` and then a short descriptive name  
  *ex:* `hotfix/duplicate_mission`
- **`release`**: must be `release/x.y.z`   
  *ex:* `release/1.2.3`

## Merge Strategy

To preserve a tree-structured, auditable project history, every merge operation
must create a merge commit. This ensures that:

- the separate history of features and bug fixes remains identifiable,
- it is always clear which commits belong to which task,
- the development process is fully auditable,
- the long-term evolution of the project remains understandable.

### Required

- `git merge --no-ff` must be used for all `feature`, `release`, and `hotfix`
  branches.  
  This guarantees that *every* merge operation results in a dedicated merge
  commit.

```
$ git checkout main
$ git merge --no-ff feature/my-feature
$ git push origin main
$ git branch -d feature/my-feature
```

### Allowed

- On local, unpublished feature branches, it is optional to use
  `git rebase -i` (to combine or rename commits).  
  Rebase is only for cleaning up *your own development work* and **must not be
  used to rewrite shared history**.

### Forbidden

- Fast-forward merges (`git merge` that does not create a merge commit),
- Any rebase on the `main`, `develop`, or `release` branches,
- Force-push (`--force`), which rewrites shared history.


## Source

- [Gist](https://gist.github.com/vxhviet/9c4a522921ad857406033c4125f343a5)
