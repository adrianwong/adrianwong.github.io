---
layout: post
comments: true
title: A tiny Git test
---

```
$ mkdir git_test && cd git_test

$ git init
Initialized empty Git repository in /Users/aw/git_test/.git/

$ echo 1 >> test_file && git add -A && git commit -m "c1"

$ git ra
* 2a5edaa  <Adrian Wong>   (HEAD -> master) c1

$ ls .git/refs/heads/ 
master

$ cat .git/refs/heads/master
2a5edaa5f4ec032c1f996bb7dd3b2c30d6413535
```

```
$ echo 1 >> test_file && git add -u && git commit -m "c2"

$ git ra
* e33e034  <Adrian Wong>   (HEAD -> master) c2
* 2a5edaa  <Adrian Wong>   c1

$ ls .git/refs/heads/ 
master

$ cat .git/refs/heads/master
e33e034c13c296b2fe1a3a7157fb72349ed659a7
```

```
$ git branch b1 && git branch b2 && git branch b3

$ git ra
* e33e034  <Adrian Wong>   (HEAD -> master, b3, b2, b1) c2
* 2a5edaa  <Adrian Wong>   c1

$ ls .git/refs/heads/
b1     b2     b3     master

$ cat .git/refs/heads/b1
e33e034c13c296b2fe1a3a7157fb72349ed659a7

$ cat .git/refs/heads/b2
e33e034c13c296b2fe1a3a7157fb72349ed659a7

$ cat .git/refs/heads/b3
e33e034c13c296b2fe1a3a7157fb72349ed659a7
```

Conclusion: A branch is just a named reference to a commit. This is important!

(Note: `git ra` is an alias I use for pretty printing all reachable references. See: [dotfiles](https://github.com/adrianwong/dotfiles){:target="_blank"}).

```
$ git checkout b1
$ echo 1 >> test_file && git add -u && git commit -m "c3"
$ echo 1 >> test_file && git add -u && git commit -m "c4"

$ git checkout b2
$ echo 1 >> test_file && git add -u && git commit -m "c5"
$ echo 1 >> test_file && git add -u && git commit -m "c6"

$ git checkout b3
$ echo 1 >> test_file && git add -u && git commit -m "c7"
$ echo 1 >> test_file && git add -u && git commit -m "c8"

$ git checkout master
$ git ra
* c80773e    <Adrian Wong>   (b3) c8
* 0b55075    <Adrian Wong>   c7
| * 331b184  <Adrian Wong>   (b2) c6
| * 200f821  <Adrian Wong>   c5
|/  
| * a7a601a  <Adrian Wong>   (b1) c4
| * 4081b44  <Adrian Wong>   c3
|/  
* e33e034    <Adrian Wong>   (HEAD -> master) c2
* 2a5edaa    <Adrian Wong>   c1

$ cat .git/refs/heads/b1
a7a601a03d1aee0d4137510c4b63de8967ee5fba

$ cat .git/refs/heads/b2
331b18454bca7b3997f52a11a62526c97da01ff4

$ cat .git/refs/heads/b3
c80773e64d8fd6da5e6ddaa41c237c3ea0a0c938
```

```
$ git branch -D b3

$ ls .git/refs/heads/
b1     b2     master

$ git ra
* 331b184    <Adrian Wong>   (b2) c6
* 200f821    <Adrian Wong>   c5
| * a7a601a  <Adrian Wong>   (b1) c4
| * 4081b44  <Adrian Wong>   c3
|/  
* e33e034    <Adrian Wong>   (HEAD -> master) c2
* 2a5edaa    <Adrian Wong>   c1
```

Conclusion: Deleting a branch removes the named reference to a commit. Commits on the deleted branch are therefore unreachable (but may still be recoverable through the `reflog`).

```
$ git checkout b2
$ git tag "some-tag"

$ git checkout master
$ git branch -D b2

$ ls .git/refs/heads/
b1     master

$ ls .git/refs/tags
some-tag

$ git ra
* 331b184    <Adrian Wong>   (tag: some-tag) c6
* 200f821    <Adrian Wong>   c5
| * a7a601a  <Adrian Wong>   (b1) c4
| * 4081b44  <Adrian Wong>   c3
|/  
* e33e034    <Adrian Wong>   (HEAD -> master) c2
* 2a5edaa    <Adrian Wong>   c1
```

Conclusion: A `tag` is also a named reference. A tagged commit is therefore still reachable even if the branch it was on has been deleted. Remember that a branch is just a named reference to a commit.

```
$ git checkout master
$ echo 1 >> test_file_2 && git add -A && git commit -m "c9"

$ git ra
* 9883dec    <Adrian Wong>   (HEAD -> master) c9
| * 331b184  <Adrian Wong>   (tag: some-tag) c6
| * 200f821  <Adrian Wong>   c5
|/  
| * a7a601a  <Adrian Wong>   (b1) c4
| * 4081b44  <Adrian Wong>   c3
|/  
* e33e034    <Adrian Wong>   c2
* 2a5edaa    <Adrian Wong>   c1

$ git merge b1
Merge made by the 'recursive' strategy.
 test_file | 2 ++
 1 file changed, 2 insertions(+)

$ git ra
*   b863ecd  <Adrian Wong>   (HEAD -> master) Merge branch 'b1'
|\  
| * a7a601a  <Adrian Wong>   (b1) c4
| * 4081b44  <Adrian Wong>   c3
* | 9883dec  <Adrian Wong>   c9
|/  
| * 331b184  <Adrian Wong>   (tag: some-tag) c6
| * 200f821  <Adrian Wong>   c5
|/  
* e33e034    <Adrian Wong>   c2
* 2a5edaa    <Adrian Wong>   c1

$ git branch -d b1

$ ls .git/refs/heads/
master

$ git cat-file -p b863ecd
tree 266c61d69fba7f528e4c64a9a36ac08ab72ac475
parent 9883deca143da271ac33144e4f9242f784826c22
parent a7a601a03d1aee0d4137510c4b63de8967ee5fba
```

Conclusion: Once a target branch is merged into the current branch, all commits on the target branch are reachable (via the merge commit) even if the branch is deleted. Remember that a branch is just a named reference to a commit.

Mega conclusion. Repeat the mantra:
* A branch is just a named reference to a commit.
* A branch is just a named reference to a commit.
* A branch is just a named reference to a commit.
