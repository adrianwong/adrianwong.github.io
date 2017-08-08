---
layout: post
comments: true
title: Incorporating semantic commit messages into my git workflow
redirect_from: /2017/06/28/semantic-commit-messages/
---

At work, I recently came across a curiosity while browsing another team's git repository. Their commit messages had these recurring, single-word descriptors that I hadn't seen before. An example:

```
chore: Clean up .gitignore files.
fix: Fixed incorrect gradle command in Jenkinsfile.
```

A cursory Google search yielded this: [semantic commit messages](https://seesparkbox.com/foundry/semantic_commit_messages){:target="_blank"}. In a nutshell, additional structure is imposed on a commit by giving it a type: `chore, docs, feat, fix, refactor, style` or `test`. Definitions can be found [here](http://karma-runner.github.io/1.0/dev/git-commit-msg.html){:target="_blank"}.

I thought the motivation behind this was pretty sound, so I gave it a whirl. It's been pretty effective so far - the added rigidity has forced (encouraged?) me to make atomic commits that are (hopefully) more clearly understood. Not a bad thing!

The write-up below is how I've incorporated semantic commit messages into my git workflow.

I first added a helper function to an existing `.githelpers` file, with very basic error-checking:

```bash
#!/bin/bash

semantic_commit_message() {
    if [ -n "$1" ] && [ -n "$2" ]; then
        git commit --message "$1: $2"
    else
        echo "usage: semantic_commit_message \"type\" \"message\""
    fi
}
```

Then I created some aliases in my `.gitconfig` file:

```bash
[alias]
    # Semantic commit messages.
    # Alias convention: 'c' for 'commit', followed by the first
    # two letters of the commit type.
    scm = "!. ~/.githelpers && semantic_commit_message"
    cch = !git scm "chore"
    cdo = !git scm "docs"
    cfe = !git scm "feat"
    cfi = !git scm "fix"
    cre = !git scm "refactor"
    cst = !git scm "style"
    cte = !git scm "test"
```

Usage:

```
$ git cfe "Add semantic commit message aliases"
[master e189a7a] feat: Add semantic commit message aliases

$ git cst "Insert newlines at EOF"
[master cf9b763] style: Insert newlines at EOF
```

The dotfiles mentioned above can be found [here](https://github.com/adrianwong/dotfiles){:target="_blank"}.
