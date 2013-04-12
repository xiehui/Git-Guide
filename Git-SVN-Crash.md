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
