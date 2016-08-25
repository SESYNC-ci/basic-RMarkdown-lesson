---
---

## Review of the Centralized Workflow

In the model for collaboration on project files introduced previously, individual researchers worked on copies of a shared project repository.

![]({{ site.baseurl }}/images/atlassian_workflow.svg){:width="40%"}

<!--split-->

A **commit** is a collection of changes to one or more project files that one person decides to add to the project.

- The **origin** repository holds the published commits to the project.
- Your local **clone** of the origin shows your working version of the project.
- You can **pull** commits from the origin to your clone to catch up.
- You can **push** commits to the origin to share your progress.

<!--split-->

### Pull

![]({{ site.baseurl }}/images/atlassian_after_pull.svg){:width="50%"} 

<!--split-->

### Push

![]({{ site.baseurl }}/images/atlassian_after_push.svg){:width="50%"} 

<!--split-->

### Project History

Commits that have made it to the **origin** are displayed on GitHub.

The [test repository](https://github.com/itcarroll/test) that I created during our introduction to `git` is public, so anyone can look at the history.

A local clone that is up-to-date with the origin will have the same history viewable at the command line with `git log`.

<!--split-->

### Reverting History

Any commit to the project can be undone.

~~~
git revert eed1f38
~~~
{:.input}

If you are unlucky enough to fall into the clutches of `vim`, you can escape with `ESC :q! RETURN`.
To avoid the editor altogether, pass the `--no-edit` argument.

~~~
git revert --no-edit eed1f38
~~~
{:.input}

<!--split-->

~~~
master [%new commit id%] Revert "adds bullet Point"
 1 file changed, 1 deletion(-)
~~~
{:.output}

The command `git revert` adds a new commit to the local clone that undoes whatever the specified commit had previously changed.

<!--split-->

Question
: What still needs to happen before the bad commit is "officialy" reverted?

<!--split-->

### Diverging Repositories

The **origin** repository and a local **clone** will naturally diverge while you work on your project. This occurs when a person makes commits to a local clone befor pulling commits made by a collaborator that were pushed onto the origin.

~~~
git push
~~~
{:.input}

~~~
To https://github.com/itcarroll/test.git
 ! [rejected]        master -> master (fetch first)
 error: failed to push some refs to 'https://github.com/itcarroll/test.git'
 hint: Updates were rejected because the remote contains work that you do
 hint: not have locally. This is usually caused by another repository pushing
 hint: to the same ref. You may want to first integrate the remote changes
 hint: (e.g., 'git pull ...') before pushing again.
~~~
{:.output}

Take time to read `git` messages -- they give good explanations of what to do!

<!--split-->

![]({{ site.baseurl }}/images/atlassian_merge.svg){:width="50%"} 

<!--split-->

### Take the Hint!

~~~
git pull
~~~
{:.input}

~~~
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/itcarroll/test
   15bc488..26c2dcd  master     -> origin/master
   Auto-merging README.md
   Merge made by the 'recursive' strategy.
    README.md | 1 +
	1 file changed, 1 insertion(+)
~~~
{:.output}

<!--split-->

### Merge works Line by Line

The last message told about changes made by this **merge commit**, which seamlessly integrates changes to the same file by multiple authors.

![]({{ site.baseurl }}/images/git_merge_line_by_line.jpg){:width="50%"}

<!--split-->

Exercise (option 1)
: Together with your team, get everyone's local clone up to date with the origin of your `data2doc` project repository.

Exercise (option 2)
: Follow the [GitHub bootcamp](https://help.github.com/articles/create-a-repo/) on creating a free **public** GitHub repository.
