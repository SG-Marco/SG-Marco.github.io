---
layout : 'single'
title: 'github와 local 차이 있을시 push / pull 오류'
---

### ! [rejected]        main -> main (non-fast-forward)

git push 를 했을 때 위와같은 오류가 나는 경우가 있다.
이것은 github안의 repo에 있는 내용과 local의 있는 내용이 달라 push가 되지 않는 경우에 나타난다.

```markdown
NOTE ABOUT FAST-FORWARDS
When an update changes a branch (or more in general, a ref) that used to point at commit A to point at another commit B, it is called a fast-forward update if and only if B is a descendant of A.

In a fast-forward update from A to B, the set of commits that the original commit A built on top of is a subset of the commits the new commit B builds on top of. Hence, it does not lose any history.

In contrast, a non-fast-forward update will lose history. For example, suppose you and somebody else started at the same commit X, and you built a history leading to commit B while the other person built a history leading to commit A. The history looks like this:

      B
     /
 ---X---A
Further suppose that the other person already pushed changes leading to A back to the original repository from which you two obtained the original commit X.

The push done by the other person updated the branch that used to point at commit X to point at commit A. It is a fast-forward.

But if you try to push, you will attempt to update the branch (that now points at A) with commit B. This does not fast-forward. If you did so, the changes introduced by commit A will be lost, because everybody will now start building on top of B.

The command by default does not allow an update that is not a fast-forward to prevent such loss of history.

If you do not want to lose your work (history from X to B) or the work by the other person (history from X to A), you would need to first fetch the history from the repository, create a history that contains changes done by both parties, and push the result back.

You can perform "git pull", resolve potential conflicts, and "git push" the result. A "git pull" will create a merge commit C between commits A and B.

      B---C
     /   /
 ---X---A
Updating A with the resulting merge commit will fast-forward and your push will be accepted.

Alternatively, you can rebase your change between X and B on top of A, with "git pull --rebase", and push the result back. The rebase will create a new commit D that builds the change between X and B on top of A.

      B   D
     /   /
 ---X---A
Again, updating A with this commit will fast-forward and your push will be accepted.

There is another common situation where you may encounter non-fast-forward rejection when you try to push, and it is possible even when you are pushing into a repository nobody else pushes into. After you push commit A yourself (in the first picture in this section), replace it with "git commit --amend" to produce commit B, and you try to push it out, because forgot that you have pushed A out already. In such a case, and only if you are certain that nobody in the meantime fetched your earlier commit A (and started building on top of it), you can run "git push --force" to overwrite it. In other words, "git push --force" is a method reserved for a case where you do mean to lose history.
```

해결방법은 pull을 먼저 해서 내용을 병합한 후에 다시 push 하라 이다.
그러면 sourcetree든 터미널에서든 pull을 해야 하는데 여기서 또 오류가 발생한다.

+ fatal: refusing to merge unrelated histories

이 오류 해결을 위해선

git pull origin (branch name) --allow-unrelated-histories
을 해야 한다. sourcetree엔 이 옵션이 없기 때문에 터미널에서 해야 하고 성공적으로 수행되면 다시 push를 하면 완료된다.



