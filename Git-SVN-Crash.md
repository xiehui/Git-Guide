## Git - SVN Crash Course

### Commiting
For the first introduction, let's make your project tracked by Git and see how we get around to do daily development in it. Let's cd to the directory with your project and initialize a brand new Git repository with it:

    git init                        svn admin create repo
    git add .                       svn import file://repo
    git commit   
                
git init will initialize the repository, git add . will add all the files under the current directory and git commit will create the initial import, given that repositories are coupled with working copies.

Now your tree is officially tracked by Git. You can explore the .git subdirectory a bit if you want, or don't if you don't care. Do some random changes to your tree now - poke into few files or such. Let's check what we've done:

    git diff                        svn diff | less
    
That's it. This is one of the more powerful commands. To get a diff with an specific revision and path do:

    git diff rev path               svn diff -rrev path

Git embeds special information in the diffs about adds, removals and mode changes:

    git apply                     	patch -p0

That will apply the patch while telling Git about and performing those "meta-changes".

There is a more concise representation of changes available:

    git status                    	svn status

This will show the concise changes summary as well as list any files that you haven't either ignored or told Git about. In addition, it will also show at the top which branch you are in.

While we are at the status command, over time plenty of the "Untracked files" will get in there, denoting files not tracked by Git. Wait a moment if you want to add them, run git clean if you want to get rid of all of them, or add them to the .gitignore file if you want to keep them around untracked (works the same as the svn:ignore property in SVN).

To restore a file from the last revision:

    git checkout path              svn revert path

You can restore everything or just specified files.

So, just like in SVN, you need to tell Git when you add, move or remove any files:

    git add file                    svn add file
    git rm file                     svn rm file
    git mv file                     svn mv file 	                  
    
You can also recursively add/remove whole directories and so on; Git's cool!

So, it's about time we commit our changes. Big surprise about the command:

    git commit -a                 	svn commit

to commit all the changes or, as with Subversion, you can limit the commit only to specified files and so on. A few words on the commit message: it is customary to have a short commit summary as the first line of the message, because various tools listing commits frequently show only the first line of the message. You can specify the commit message using the -m parameter as you are used, but you can pass several -m arguments and they will create separate paragraphs in the commit message:

If you don't pass any -m parameter or pass the -e parameter, your favorite $EDITOR will get run and you can compose your commit message there, just as with Subversion. In addition, the list of files to be committed is shown.

And as a bonus, if you pass it the -v parameter it will show the whole patch being committed in the editor so that you can do a quick last-time review.

By the way, if you screwed up committing, there's not much you can do with Subversion, except using some enigmatic svnadmin subcommands. Git does it better - you can amend your latest commit (re-edit the metadata as well as update the tree) using git commit --amend, or toss your latest commit away completely using git reset HEAD^, this will not change the working tree.


### Browsing

Now that we have committed some stuff, you might want to review your history:

    git log                         svn log | less
    git blame file                  svn blame file         

The log command works quite similar in SVN and Git; again, git log is quite powerful, please look through its options to see some of the stuff it can do.

The blame command is more powerful as it can detect the movement of lines, even with file copies and renames. But there is a big chance that you probably want to do something different! Usually, when using annotate you are looking for the origin of some piece of code, and the so-called pickaxe of Git is much more comfortable tool for that job (git log -Sstring shows the commits which add or remove any file data matching string).

You can see the contents of a file, the listing of a directory or a commit with:

    git show rev:path/to/file                   svn cat url
    git show rev:path/to/directory              svn list url
    git show rev 	                            svn log -rrev url
                                                svn diff -crev url
                                                
### Tagging and branching

Subversion marks certain checkpoints in history through copies, the copy is usually placed in a directory named tags. Git tags are much more powerful. The Git tag can have an arbitrary description attached (the first line is special as in the commit case), some people actually store the whole release announcements in the tag descriptions. The identity of the person who tagged is stored (again following the same rules as identity of the committer). You can tag other objects than commits (but that is conceptually rather low-level operation). And the tag can be cryptographically PGP signed to verify the identity (by Git's nature of working, that signature also confirms the validity of the associated revision, its history and tree). So, let's do it:

    git tag -a name                            svn copy http://example.com/svn/trunk http://example.com/svn/tags/name

To list tags and to show the tag message:

    git tag -l                                  svn list http://example.com/svn/tags/
    git show tag 	                            svn log --limit 1 http://example.com/svn/tags/tag
    
Like Subversion, Git can do branches (surprise surprise!). In Subversion, you basically copy your project to a subdirectory. In Git, you tell it, well, to create a branch.

    git branch branch                           svn copy http://example.com/svn/trunk http://example.com/svn/branches/branch
    git checkout branch 	                    svn switch http://example.com/svn/branches/branch

The first command creates a branch, the second command switches your tree to a certain branch. You can pass an extra argument to git branch to base your new branch on a different revision than the latest one.

You can list your branches conveniently using the aforementioned git-branch command without arguments the listing of branches. The current one is denoted by an "*".
git branch 	svn list http://example.com/svn/branches/

To move your tree to some older revision, use:

    git checkout rev                            svn update -r rev
    git checkout prevbranch 	                svn update

or you could create a temporary branch. In Git you can make commits on top of the older revision and use it as another branch.

### Merging
Git supports merging between branches much better than Subversion - history of both branches is preserved over the merges and repeated merges of the same branches are supported out-of-the-box. Make sure you are on one of the to-be-merged branches and merge the other one now:

    git merge branch                            (assuming the branch was created in revision 20 and you are inside a working copy of trunk)
                                                svn merge -r 20:HEAD http://example.com/svn/branches/branch

If changes were made on only one of the branches since the last merge, they are simply replayed on your other branch (so-called fast-forward merge). If changes were made on both branches, they are merged intelligently (so-called three-way merge): if any changes conflicted, git merge will report them and let you resolve them, updating the rest of the tree already to the result state; you can git commit when you resolve the conflicts. If no changes conflicted, a commit is made automatically with a convenient log message (or you can do git merge --no-commit branch to review the merge result and then do the commit yourself).

Aside from merging, sometimes you want to just pick one commit from a different branch. To apply the changes in revision rev and commit them to the current branch use:
    git cherry-pick rev                     	svn merge -c rev url

### Going Remote
So far, we have neglected that Git is a distributed version control system. It is time for us to set the record straight - let's grab some stuff from remote sites.

If you are working on someone else's project, you usually want to clone its repository instead of starting your own. We've already mentioned that at the top of this document:
    
    git clone url                            svn checkout url

Now you have the default branch (normally master), but in addition you got all the remote branches and tags. In clone's default setup, the default local branch tracks the origin remote, which represents the default branch in the remote repository.

Remote branch, you ask? Well, so far we have worked only with local branches. Remote branches are a mirror image of branches in remote repositories and you don't ever switch to them directly or write to them. Let me repeat - you never mess with remote branches. If you want to switch to a remote branch, you need to create a corresponding local branch which will "track" the remote branch:

    git checkout --track -b branch origin/branch 	    svn switch url

You can add more remote branches to a cloned repository, as well as just an initialized one, using git remote add remote url. The command git remote lists all the remotes repositories and git remote show remote shows the branches in a remote repository.

Now, how do you get any new changes from a remote repository? You fetch them: git fetch. At this point they are in your repository and you can examine them using git log origin (git log HEAD..origin to see just the changes you don't have in your branch), diff them, and obviously, merge them - just do git merge origin. Note that if you don't specify a branch to fetch, it will conveniently default to the tracking remote.

Since you frequently just fetch + merge the tracking remote branch, there is a command to automate that:
    git pull                                     	svn update
