# Table of Contents

# Overview
During your early software development journey you most likely thought of or personally experienced these all of these issues before.

- You are working on an existing project. You make complex changes to the code only to find out that after all your hard work, the program breaks. You spend hours trying to reset the code back to the state it was in before you made all your edits. If you were prudent, you may have made a backup zip file of your project and you would load that up and start fresh. Is there a less manual and more convenient way to backup and restore your code?

- Your code is stuck on your local machine, but you want to be able to access it from other devices, or even have other users, like your instructor or professor, access it.

- You want to have more than one person or even a small team work on a project with you, but how are you possibly going to manage all those users working on the same code at the same time? How do companies make sure edits made by a dev team of 10+ employees don't conflict and overwrite each other?

Version control applications like Git provide solutions to all of these problems and more. Applications like Git will often be used to upload and manage changes to code on a cloud service like Github where programmers can store and share their code on the internet. As a software developer in the 21st century, it is essential that you learn how to use both of these services for collaborative programming and versioning as you will very likely use them on the job or in your college courses.

Using git can be very easy or slightly complicated (requiring some interaction with the bash command line) depending on your development environment. Many IDEs like Visual Stuido Code and IntelliJ have git commands fully integrated into their UIs so that you will never even have to touch the command line to upload your code / code changes to github. The downside to using an IDE's user interface to perform git actions is that the steps to get your code onto Github can vary widely between IDEs and even different versions of IDEs. Also, you may not be able to get a clear vision of what git commands are happening under the hood, making it more likely for you to perform unintended actions and more difficult for you to troubleshoot errors. For this reason, I recommned learning how to use Git to manage your code via Github using the Git command line rather than your IDE.

**DISCLAIMER**: This file was purely created to be my personal reference and serve as a comprehensive record of the knowledge I gained. As such, it is not intended to be a 100% correct reference on these topics and there may be errors throughout the text. It was written in a way to facilitate learning however, and some examples may come from various textbooks, courses, videos, discussion forums, and online articles that I came across while studying this subject.

# Installation and Setup

## Git

- Windows: install the exe located in the git website. I recommend installing Git bash rather than Git for the cmd. To ensure git was successfully downloaded go to the cmd and type git.

>[Install Git Windows](https://git-scm.com/download/win)

- MacOS: git is installed on your system by default. Run the command below to check.

```bash
git --version
```

- Linux Debian/Ubuntu: use apt-get

```bash
apt-get install git
```

## Github
- Unlike Git, Github is an online service, so navigate to the Github website and create an account. That's all you need to worry about for now. Creating repositories and uploading your code will be covered in the next sections.

> [Github Website](https://github.com/)

# Creating a New Github Repository

- There are two methods to start a github project from scratch

1. By creating a repository on github first, then creating your project in an editor, and then uploading your project onto that github repository that you just created.

2. By creating your project in an editor first and then uploading your project to github

> It is good practice to connect your project to github after you have completed configuring your project and **before** you write any actual code. This way, your project is ready to use git and github features like versioning from the getgo.

> Another common scenario is that you may want to take an existing project on Github and put it into your local machine for you to work on, but we will cover this later. 

## Method1: Create Github Repository First

- Go to Github and hit create new repository. Give it the name of your project. You will have no files in it for now but you will be asked to create the README.md. Place some placeholder text in there and click `Commit Changes`. Be sure commit to master is selected. We will explain later what terms like "master" and "branch" refer to.

- If you are using an IDE like Intellij, Eclipse, or Pycharm that provides a pre-made or auto-generated project structure for you, don't continue with the steps below and skip ahead to the next section. If you are using an editor like VSCode or Atom that does not generate any project boilerplate code, continue below.

- Next, go to your favorite code editor. It will most likely have the option to open a terminal, if not you will have to use the terminal supplied by your OS to manually navigate to your project's root directory (the toppest level directory of your project's filestructure). When opening the terminal in an IDE, the terminal should automatically open pointing to your project's root directory. If your command line is not pointing to your project's root directory, unexpected errors and behavior could occur.

- In the command line you are going to type a git command that will `clone` the repository you just made on Github into your project (your local machine).

- the address in the last part of this command immediately following clone is the `SSH` key of your repository. It can be found by navigating to your repository page on Github, clicking the green button that says "code", selecting SSH instead of HTTP, and then copying the text field.

```bash
git clone git@github.com:username/demo-repository.git
```
- After executing the above command, you should see the repository's files appear in your project's filestructure. Congratulations, you have successfully created a fresh new project that is fully linked to Github. Next, we will go over how to make changes to the project and keep the changes synced with the linked github repository. Skip the next section on IDEs and go to the SSH section below it.

### Using IDEs

- If using an IDE like Intellij which allows you create "projects" that generate a lot of additional files on startup, it may be less convenient to use the command line to pull your empty github repository with just the README.md.

- Instead, when creating a new project, look for an option that says something along the lines of "create new project from VCS" or "new project form version control". You will likely be asked to input the repository's HTTP url or SSH security key and the IDE will do the import work for you.

- Alternatively it may be easier to use method 2 described above. Simply create your project within the IDE as normal, and look around the menu options for something like Git or Version Control. There should be an option to share your project on Github or something similar. You will be prompted to link your github account to the IDE and give the repository a name.


## Method 2: Upload Existing Project to Github
- If you are a complete beginner to Git and Github, I recommend you read through method 1 first because I will gloss over some finer details, definitions, and wording and assume you already know how to navigate basic Github functions.

- If you do not wish to use the Git command line and are using an IDE, refer to the section just above this one. You may at least want to read the parts in this section that cover what a remote is.

- Open your project in your code editor and type this in the command line to declare your project as a git project. As a good practice, ensure that there is at least a README.md in your project before you do this step.

```bash
git init
```
- Now create the repository on github just as we did in method 1 and get its SSH key. Use it in the command below so that git knows which repository to associate with your project.

```bash
git remote add origin git@github.com:username/demo-repository.git
```
- a `remote` is just a location where your project is going to be uploaded to and versioned. A remote does not have to be a Github repository, it could be just another folder in your computer not managed by Github. You can have multiple remotes connected to your project.

- Use this command to see a list of the remotes your project is conencted to.

```bash
git remote -v
```
- Congratulations, your existing project has now been fully connected to Github

# Connect Local Machine to Github With SSH
- If you are using an IDE or Github Desktop, you may skip this section entirely. The IDE will deal with all of the authentication for you by asking you to allow access to your Github account.

- For those of you who want to learn how to use the Git command line, this section might be the hardest for beginners to grasp, but learning how to SSH into web services from your desktop securely is an important skill that may come up various times throughout your software development career.

- Before you are allowed to "push" any changes to your project onto your Github repository, you are required to prove to Github that you are the owner of the repository by using an SSH key.

## Generating an SSH Key
- Exit your development environment. Go to the terminal (or git bash if using Windows) and enter this command using the email linked to your Github account. If you must know the details, `-t rsa` specifies the encryption type and `-b 4096` specifies the strength, which in this example is SHA256.

```bash
ssh-keygen -t rsa -b 4096 -C "email@example.com"
```

- You will be prompted to assign a name to the key. You can give the key a passphrase or opt to leave it empty. After finishing the configuration, you will recieve a message saying "your public key has been saved in keyname.pub"

- Locate the directory where your keys were saved. You will see two keys, one with a .pub extension and one without. The .pub extension signifies that the key is a public key and is the one that will be uploaded to your Github. The key without the .pub extension is your private key and should be stored in a safe location on your local machine.

- open the public key using the cat command and copy its contents.

```bash
cat mypublickey.pub
```

- Go to settings on your github profile and navigate to SSH and GPG keys. Click on add new key and paste the contents of your key into the text field

## Adding a Key to Your SSH Agent

- Next is the difficult part, which is configuring git command line so that it recognizes the key you just generated.

- Even as a command line user, you can skip this step entirely if you have the Github desktop installed, which you can get here. If the following steps do not work out, I recommend using this option instead. 

> [Github Desktop](https://desktop.github.com/)

- first enter this command in your terminal or git bash if using Windows.

```bash
eval $(ssh-agent -s)
```

- This starts an ssh-agent, now enter this

```bash
Agent pid 59566
```

- Now add your private key to the SSH agent where myprivatekey is the name of your key

```bash
ssh-add ~/.ssh/myprivatekey
```

- if all went well, now you have all the necessary security clearance to push changes you make in your project to github.


# Committing Changes to a  Repository

- Now that we know how to make a Github project, let's go over the most important thing: submitting changes to your repository. 

- It is important to understand that just because your project is now linked to Github does not mean that any edits you make in your project will be automatically reflected on Github. Nowdays, there may be some cases in which this can be configured, but in most cases you will need to undergo a three part process to manually declare and submit your changes to Github.

- in Git language, whenever you want to submit changes to Github, you do three actions: **stage**, **commit**, and **push**. 

## Stage

- the **stage** phase is preliminary step where you declare which changes you made to your project that you want to submit to Github. You can see this phase as as previewing your changes before you submit them to Github. In most cases, you will want to submit all changes, but in some cases you may only want to submit one file that you changed and not all of them.

- It is adviseable to first use the git command line to see a list of all changes made to the project since the last time you submitted the project. Use the git `status` command

```bash
git status
```

- This command will print a list changes organized into two categories: modified or untracked. Newly created files since the last submission will appear in the untracked category while existing files that were updated will appear in the modified category.

- In order to add the untracked files to the Github repository or submit the modifications to the existing files, you must first use the `add` command. Use the period character to add all the untracked files. This will also stage all the changes in the modified section.

```bash
git add .
```

- If you only want to add / stage a specific file, use the file name instead of the period character. In most cases however, you will want to stage all changes and new files using the period character (wildcard character)

```bash
git add index.html
```

## Commit

- Once all your desired modifications to the project are staged, you will want to **commit** them. Committing changes is like telling git that you have staged your changes and are prepared to submit them. Committing changes **does not** submit them to Github, that happens in the Push phase.

- Commit using the command line like so. Always include a descriptive message in your commits briefly explaining what you changed, why you made those changes, and perhaps information to describe the state of your program.

```bash
 git commit -m "describe changes" -m "additional descriptions"
```
- Again, at this stage we have only made our changes locally, not globally. Github does not yet see our changes.


## Push

- This is the stage where you finally submit your changes to the remote repository. Type the command below in the git command line to push.

```bash
git push origin master
```

- It is possible that in future versions of git, `master` will be replaced by `main`.

```bash
git push origin main
```

> If you run into any errors running these commands, you may have not configured your SSH keys yet. Refer to the section above to learn how to authenticate your computer's connection to Github using SSH.

- if you really do not want to type out origin master every time you push, you can set a default push or `upstream` using the -u option.

```bash
git push -u origin master
```

- now you only have to type this whenver you want to push

```bash
git push -u
```

- you can always reassign your upstream, but if you are a beginner and not working with branches, it is likely not necessary.

## IDEs
- in an IDE, staging may be incorporated into the commit menu option instead.

- When working with a project linked to Github, you may see an option to commit under a menu title something like Git or Version Control. This will typically pull up a visual list of changes you have made that can be checked and unchecked as well as a message box where you can put your commit message. You may then be able to commit or even commit and push all at once.

- As with commit, in an IDE you will likely have a menu option to push your committed changes. Your IDE may ask you to supply your github credentials to ensure that you are the owner of the repository.
