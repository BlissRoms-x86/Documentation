# Maintaining Proper Authorship

One of the most important things you need to keep in mind while working on open-source projects is maintaining correct authorship. In this article, we'll show you why maintaining proper authorship is important, give you a couple examples on correct and incorrect commits, and show you the overall procedure of correctly pulling in commits from others.

### What is "kanging"?

\[[https://www.urbandictionary.com/define.php?term=kanged](https://www.urbandictionary.com/define.php?term=kanged) Kanging\] is a term used in the Android development community for the action of passing off someone else's code as one's own, intentionally or unintentionally.

### Why is kanging bad?

Kanging is bad because the developers who worked hard on the commits do not get the recognition they deserve. Over time, this may cause the developer to quit releasing public source code or even retire from the Android development community. This has definitely happened before!

### Kanging examples \(what you should avoid doing\)

'''Example 1:''' You're trying to cherry-pick some commits from a different repository, but keep running into `git merge` issues. Out of frustration, you copy the code from the commit, and then just commit it using `git commit -a`. Satisfied, you push it up to GitHub.

'''Example 2:''' You bring up a bunch of commits, and squash them before pushing to GitHub.

'''Example 3:''' You intentionally want to pass off another developer's work as your own. You cherry-pick the commit, and then amend the commit to rewrite the author information.

Let's go over why this is wrong. Example 1 is an example of an unintentional kang. You didn't want to resolve the `git merge` issues, so decided to just copy the code and commit it as your own. This is bad because the author information does not get transferred over with your copy, which you have to specify manually.

Example 2 is more of an accident. If you squash multiple commits, all authorship information for the range of commits is lost. In addition, it becomes a real headache for other developers if something in the range of your commits is wrong. Because you cannot individually revert commits in a squash, squashing is very much discouraged and should ONLY be used when you have a lot of commits that you committed yourself and are small in nature.

Example 3 is an example of an intentional kang. We won't explain why because it should be fairly obvious.

### How to maintain proper authorship

The process is fairly simple yet important to understand.

If you are cherry-picking commits, the authorship information is transferred automatically. Provided that you are running `git cherry-pick`, the entire commit information, down to when the commit was created, is picked into your repository. You don't have to do anything in this case.

If you are committing someone else's code yourself, then you must manually specify who the author is. There are a lot of reasons why you would do this, from merge issues to incompatible code with the existing codebase. To manually specify an author, follow the \[\[\#Manually specifying an author \| Manually specifying an author section below\]\].

Finally, do NOT squash a range of commits that are not your own. This completely wipes authorship information from the range of commits and causes a massive headache for other developers.

### Manually specifying an author

You need to first determine the original author's name and email address.

GitHub no longer shows the author information when you mouse over the profile picture, which is quite unfortunate. However, there is an easy workaround.

Go to the commit that you want to pick. \[[https://github.com/BlissRoms/Documentation-release/commit/5ae1c6c4441786cd3ad5bd1773c831ba13cd86bc](https://github.com/BlissRoms/Documentation-release/commit/5ae1c6c4441786cd3ad5bd1773c831ba13cd86bc) We'll use my commit as an example.\]

Add the word `.patch`, with the period, to the end of the URL and press Enter to navigate to the raw patch.

In the patch, find the section that contains the author. It should be at the top of the page.

Now, it's time to commit with the correct author information. Make the necessary changes, and then commit using this command:

```text

git commit --author="AUTHOR_INFORMATION_HERE"
```

Following the example, I would write:

```text

git commit --author="Eric Park "
```

Once done, push to GitHub or Gerrit.
