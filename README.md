# This is a walkthrough of how to do some basic things in Git

On GitHub/GitLab make a new repo called `my-repo`.
Use the web GUI to copy the URL to your clipboard and then clone it to your local machine
~~~bash
git clone <the URL you copied>
cd my-repo
~~~

GitHub                      |  GitLab
:-------------------------: | :-------------------------:
![copy URL GitHub](/images/copyURL_GitHub.png)  |  ![copy URL GitLab](/images/copyURL_GitLab.png)

Create a `README` and [license](http://choosealicense.com/) for your repo
~~~bash
touch README.md
# edit README as desired
touch LICENSE
# edit LICENSE as desired
~~~
Add and commit the files
~~~bash
git add README.md
git add LICENSE
git commit -m "add README and LICENSE"
# have the changes be tracked
git push -u origin master
~~~
# Toy example of a workflow
Create a development branch to do work in
~~~bash
git checkout -b dev
#git push -u origin dev
~~~
Make an edit to the `README` such as
~~~bash
This is a local edit to the README!
~~~
commit the change
~~~bash
git add README.md
git commit -m "Edit the README locally"
~~~
We're going to purposely make a merge conflict later by going to GitHub/GitLab and making
an edit to the `README` on the `master` branch on there
~~~bash
This is an upstream edit to the README from the web!
~~~

GitHub                      |  GitLab
:-------------------------: | :-------------------------:
![upstream edit GitHub](/images/upstream-edit_GitHub.png) | ![upstream edit GitHub](/images/upstream-edit_GitLab.png)

On your local machine switch to master branch
~~~bash
git checkout master
~~~
merge the changes made in `dev` onto `master`
~~~bash
git merge dev
~~~
Before pushing changes make sure that there were no changes already made, so `fetch`
and `merge`
~~~bash
git fetch
git merge origin/master
~~~
* **N.B.:** What is happening here is we pulled down the current state of the entire
remote repo (`origin`) with `fetch` and then merged the changes that exist in the remote
branch (`origin/master`) onto the branch we currently have checked out (`master`)

which will result in the following merge conflict:

~~~bash
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
~~~
Edit the `README` to resolve the conflict:
~~~bash
<<<<<<< HEAD
This is a local edit to the README!
=======
This is an upstream edit to the README from the web!
>>>>>>> origin/master
~~~
to
~~~bash
This is a local edit to the README followed by an upstream edit to the README from the web!
~~~
Then commit and push
~~~bash
git add README.md
git commit -m "Resolve merge conflict"
git push origin master
~~~

# Example of Matthew's daily workflow

Every directory on my local computer that has code in it is a Git repo. A typical snapshot
of what that looks like is
~~~bash
git branch
* YYYY-MM-DD
  dev
  master
~~~
though if this repo has a remote then going to look at it will only show you `master`
and `dev`.

## Why?
The idea here is that `master` should always be production ready and `dev` should be where
the new feature that I'm working on it being tested. However, each day I don't want to
possibly contaminate `dev`. So everyday when I start work I (from the `dev` branch) do
~~~bash
git checkout -b YYYY-MM-DD
~~~
and then do all my work for the day in the `YYYY-MM-DD` branch. Then at the end of the day
I simply
~~~bash
git checkout dev
git merge YYYY-MM-DD
git branch -d YYYY-MM-DD
~~~
so dev reflects the day's improvements and as `YYYY-MM-DD` has served its purpose it is
removed.

If everything has become a raging dumpster fire (it is 2016 when I'm writing this, so
there should *really* be a raging dumpster fire emoji) I can instead do
~~~bash
git checkout dev
git branch -d YYYY-MM-DD
~~~
and walk away with no damages to try again tomorrow. :)

# Basic Git Configuration
It is very helpful to have a [`.gitconfig` file](https://git-scm.com/book/tr/v2/Customizing-Git-Git-Configuration). You can have a
global one that sets defaults on all git repositories (`~/.gitconfig`) and then repository
specific `.gitconfig` files as well.

To start with, make sure that you have a global `.gitconfig` that sets your username and
email.
~~~bash
git config --global user.name "Firstname LastName"
git config --global user.email "yourEmail@email.com"
~~~
To check the settings of your `.gitconfig` you can either just list it out
~~~bash
cat ~/.gitconfig
~~~
or you can query it for specific commands
~~~bash
git config user.name
~~~

## Helpful aliases
I would also recommend setting up the following aliases
~~~bash
git config --global alias.pl "pull --rebase"
git config --global alias.glog "log --graph --oneline --decorate --all"
~~~
