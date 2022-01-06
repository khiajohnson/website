+++
author = "Khia A. Johnson"
title = "git disseration, a quick guide to the non-writing parts of my workflow"
date = "2021-07-26"
tags = [
    "git",
    "latex",
    "dissertation"
    ]
+++

I'm currently writing my dissertation with LaTeX in a private GitHub repository. Props to [the guy on Twitter writing his dissertation in full public view](https://twitter.com/khia_johnson/status/1410464438847606786?s=20), but I'll pass. Maybe I'll make it public after my defense, and you can gaze into the crystal ball of my writing habits... 🔮✍🏼 (or more likely, adapt my analysis code). In the meantime, here's a quick guide to the non-writing parts of writing with a latex template, version control, and a cloud backup. It's certainly given me some peace of mind. You can do it, too!

 <!--more-->

If you're here, I'll assume that you are pro-LaTeX, pro-version-control, and pro-not-losing-your-files when your laptop kicks the dust following a kernel panic episode (it was a long time ago — and yes, it still gives me nightmares). I don't need to convince you that these things are good things to do. Rather, I want to show you that you are indeed capable!

## The tools
- My MacBook Air (sorry this is guide to what I do and I use a Mac)
- LaTeX, [homebrew](https://formulae.brew.sh/cask/mactex-no-gui)-ed 🍻
- The [ubc-diss template](https://github.com/briandealwis/ubcdiss)
- [VS Code](https://code.visualstudio.com/) with the [LaTeX Workshop](https://marketplace.visualstudio.com/items?itemName=James-Yu.latex-workshop) extension
- [git](https://git-scm.com/), also [homebrew](https://formulae.brew.sh/formula/git)-ed 🌱 

Among the linguists I know, LaTeX usage is relatively commonplace. VS Code is a popular program for Python users and is familiar enough for RStudio users (emphasis on enough, because RStudio is *magical*). The trickiest part is knowing to put them together, and dealing with weird LaTeX errors &mdash; a problem that I will not tackle here.

Far more linguists are either git-unaware, git-ambivalent, git-skeptical, git-averse, or something along one of those lines. I think a big reason is that git seems rather opaque and complicated at first glance. Perhaps an even bigger reason is that it's not taught. But that's not unique to linguistics! Check out [The Missing Semester of your CS Education](https://missing.csail.mit.edu/), and you'll feel better about the things you missed in your non-CS education. They have a great intro to all things command-line, shell scripting, and git. I highly recommend the first six lectures. 

## Setting up

Install all the things listed above. Then follow the instructions for [installing LaTeX Workshop](https://github.com/James-Yu/LaTeX-Workshop/wiki/Install). You might want to test it out at this point with [a basic document](https://www.overleaf.com/learn/latex/Learn_LaTeX_in_30_minutes#Writing_your_first_piece_of_LaTeX) to make sure you can compile properly. You could do that in a new project folder, which will serve you in the next step.

Open Terminal (or your preferred Unix shell). The default on Macbooks is currently a [Z Shell](https://www.zsh.org/), and if you're like me and you want it to look pretty, take a look at [Oh My Zsh](https://ohmyz.sh/), or scroll down to the "Dotfiles" header on [this lecture in the Missing Semester](https://missing.csail.mit.edu/2020/command-line/). It's a bit of a rabbit hole and unnecessary for this guide, but hey, I said I'd tell you what I do! And I went down that rabbit hole.

Navigate to the folder for your project (`cd path/to/folder`) or create a folder (`mkdir myproject`). Navigate there, and create some files, including (ideally) the following:

- A sample LaTeX document to test your installation &mdash; maybe you did this already?! I'd do [something basic](https://www.overleaf.com/learn/latex/Learn_LaTeX_in_30_minutes#Writing_your_first_piece_of_LaTeX) unless there's a particular template you'd like to have working.
- A `README.md` written in [Markdown](https://www.markdownguide.org/) will display on your repository's main page and tell people (others if you make it public... yourself if you ever forget) what the repository is about. 
- A `.gitignore` "dotfile" (a file that starts with a dot) &mdash; a crucial file that gives you control over what gets backed up in the cloud. There are many reasons you may want to do this &mdash; perhaps your data files are too big for GitHub, or maybe your ethics approval limits what you can do with your data. A typical LaTeX `.gitignore` will also exclude auxiliary and temporary files &mdash; here's a [template from GitHub](https://github.com/github/gitignore/blob/master/TeX.gitignore). Start there and add whatever is relevant to your project. I usually add lines to ignore `*.DS_Store` and any local-only folders. Once you save the file, you may wonder where it went. Finder hides dotfiles by default. To toggle seeing them, press  <command + shift + .> or open a Terminal and use the `ls` Unix command. 
- Once you have some files ready to push to the cloud, follow the instructions for [adding an existing project to GitHub using the command line](https://docs.github.com/en/github/importing-your-projects-to-github/importing-source-code-to-github/adding-an-existing-project-to-github-using-the-command-line). You may need to set up authentication, but it's a one time deal. Something I regularly forget to do: authenticate with an [access token](https://docs.github.com/en/github/authenticating-to-github/keeping-your-account-and-data-secure/creating-a-personal-access-token), *not* my password.

And voilà! You are up and running.

## The git basics

![Screenshot of command line where I typed "git dissertation" and got a response "git: 'dissertation' is not a git command. See 'git --help'.](/images/git-dissertation.png)

I tried.

In all seriousness, for working on solo projects, 99% of the time you will be using the three following `git` commands. Yes, you can do fancier things. And yes, you may need to figure out how to do those fancy things in the future when you a thanking your past self for going down the version control path. But that is not today. 

Today you need three things:

1.  `git add .`: The `.` means *everything*, and you can also specify specific files if you don't want that. This command stages your files for committing. The staging area gives you flexibility later, but for now, just treat it as a step on the way. 
2.  `git commit -m "think of this as a concise, useful note to your future self"`: This command takes everything you staged, adds a message describing what you're doing, and commits it to the repository. Think of this as saving a new version or subversion. Commit as often as you think makes sense. Just know that if you ever need to go back to how something was in the past, you'll be choosing from the points at which you committed. 
3. `git push`: This takes the version you just committed and uploads it to your online repository (GitHub or another platform like GitLab). It's also an optional part of this process. While cloud backups are great (and IMO important), they are not required for using git-based version control. 

Write. Rinse. Repeat. And that's it. Here's what this looks like in a screenshot:

![Screenshots with following command line text: git add .; git commit -m "updated lit review"; git push; git status](/images/git-basic-flow.png)

There are a couple of additional git commands that you may want to use. I snuck one into the screenshot above:

- `git status` will print out information that can help as a sanity check for those of you who want to see and confirm what's happening. I use it a lot.
- `git reset` will remove everything from staging after using `git add .`

## You can do it! 

Writing is much harder. I focused on the getting-going-with-git part of this guide, and LaTeX is another challenge entirely. The good news is that you could use this guide for writing with Markdown, basic text file, or whatever you prefer!