# How to split a repo into submodules
Source: https://blog.github.com/2016-02-01-working-with-submodules/

A `slingshot` repo contains code for `rubber-band` and `y-shaped-stick`. Another repo, `bag-of-chips` requires `rubber-band` as well. Because the same `rubber-band` code will be shared in multiple repos, the developer decides to create a separate `rubber-band` repo and add it as a submodule to the `slingshot` and `bag-of-chips` repos.


## A. Original repo I
```
  ########################################
    slingshot/
      .git/
      y-shaped-stick/
      rubber-band/
        <contents-of-rubber-band>
  ########################################
```

## B. Create repo II by copying repo I and filtering the history/contents
```
  cp -r slingshot rubber-band
  ########################################
    rubber-band/
      .git/
      y-shaped-stick/
      rubber-band/
        <contents-of-rubber-band>
  ########################################
  cd /projects/rubber-band
  git filter-branch --subdirectory-filter rubber-band -- --all
  Create new git repo
  git remote set-url origin https://github.com/<user>/rubber-band
  ########################################
    rubber-band/
      .git/
      <contents-of-rubber-band>
  ########################################
```

## C. Modify original repo I by removing repo II files and adding repo II as a submodule
```
  cd /projects/slingshot
  git rm -r rubber-band
  git commit -m "Remove rubber-band (preparing for submodule)"
  ########################################
    slingshot/
      .git/
      y-shaped-stick/
      rubber-band/
        <contents-of-rubber-band>
  ########################################
  git submodule add https://github.com/<user>/rubber-band rubber-band
  git commit -m "rubber-band submodule"
  git push
  ########################################
    slingshot/
      .git/
      .gitmodules
      y-shaped-stick/
      rubber-band/ @ asdf123
        <contents-of-rubber-band>
  ########################################
```

## D. (In the future, clone repo I with recursive flag)
```
  git clone --recursive https://github.com/<user>/slingshot
  ########################################
    slingshot/
      .git/
      .gitmodules
      y-shaped-stick/
      rubber-band/ @ asdf123
        <contents-of-rubber-band>
  ########################################
```

## E. Add repo II to another repo
```
  cd /projects/bag-of-chips
  git submodule add https://github.com/<user>/rubber-band rubber-band
  git commit -m "rubber-band submodule"
  git submodule update --init --recursive
  ########################################
    bag-of-chips/
      .git/
      .gitmodules
      chips/
      bag/
      rubber-band/ @ asdf123
        <contents-of-rubber-band>
  ########################################
```

## F. Updating submodules
If repo II changes, then in repos that reference this as a submodule must call `git submodule update --recursive --remote` to update to the latest tips of remote branches.
