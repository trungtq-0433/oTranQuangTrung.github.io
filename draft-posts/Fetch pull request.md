### Solution 1:
1. `git config -e`

Then add block code:
```
[remote "pr"]
	url = git@github:framgia/indahash-nttd.git
  fetch = +refs/pull/*/head:refs/remotes/us/*
```
All of pr will fetch in folder `.git/refs/remotes/us`

2. `git fetch pr` 

3. `git checkout <pr_id>`

### Solution 2:
1. `vi ~/.bashrc`
```
gfgc() {
  git fetch us pull/$1/head:$1 --force
  git checkout $1
}
```
2. `gfgc <pr_id>`

