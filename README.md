# My LaTeX Setup

This small setup guide is intended

- For me to collect some helpful links when working with LaTeX.
- For people in my surrounding to get started with LaTeX and Linux (WSL) if you have no prior (or few) experience in either Unix-like environments and/or LaTeX.

The setup described here has served me well during one of my semester at uni. I love VSCode and I prefer doing all coding-related stuff in Unix-like environments but still like to have all the advantages of Windows, which is why WSL is the perfect choice for me. The Latex Workshop plugin for VSCode really does a great job and you get all LaTeX-niceties you'd expect from a LaTeX editor.

The guide is not intended to be complete but rather give you some hints in the "right" direction. You will have to do some research on your own, but I've included many links which should give you a good starting point. Feel free to do it your own way, this is just my setup -- why I love -- but you might find something that works better for you.

You can give feedback by opening a new issue.



## LaTeX installation & usage guide

*This might contain some references to "PAP" which is the "Beginner training" for physicists at the Uni Heidelberg.*


### Get Windows Subsystem for Linux (WSL)

Go to the windows store and search for "Ubuntu 22". Install it. I also highly recommend to install the "Windows Terminal" from the store, it's so convenient. Inside the Windows Terminal, you should now be able to login to WSL (your Ubuntu):

![image](https://github.com/Splines/pap1/assets/37160523/810b56a0-a431-471f-ace2-6e14c1827d6c)

Make yourself at least familiar with these very basic Linux commands:
- `cd` (home directory is called `~`), you can use tab completion for the `cd` command :)
- `mv`, `mkdir`
- `ls -hl`
- `apt` (make sure you update your packages by running `sudo apt update`, then: `sudo apt upgrade`
- Use `man <command>` to find out more about the command, e.g. `man ls`. Otherwise, see the [Ubuntu docs](https://wiki.ubuntuusers.de/ls/) and search your command over there.

### Install TeXLive

Remember you once installed MikTex as LaTeX distribution on Windows? On Linux, one well-known distribution is called TeXLive. We will install it with all the packages we need through the Ubuntu `apt` package manager. See the installation choices you have [here](https://tex.stackexchange.com/a/504566). See the TeXLive installation guide [here](https://wiki.ubuntuusers.de/TeX_Live/#Installation). We will use this:

```
sudo apt install texlive texlive-lang-german texlive-latex-extra texlive-latex-science
```

Note that this will not install the newest TeXLive version but instead only uses the one provided in the [Ubuntu package registry](https://packages.ubuntu.com/), which are more oftentimes outdated. However, this doesn't bother us for the moment, as everything still works. But if you want to update later, use the [`update-tlmbr-latest.sh` script](https://tex.stackexchange.com/a/537810) (not needed for right now).

At least take a glance at [CTAN](https://www.ctan.org/) (the Comprehensive TeX Archive Network), where all LaTeX packages are hosted and where you can also find documentation for packages in PDF form, e.g. for the [`WithArrows`](https://www.ctan.org/pkg/witharrows) package.

### Get document and compile PDF

Great, you've now got a working LaTeX distribution setup that we can use.

Open VSCode and connect to WSL by pressing "F1", then typing "WSL: Connect to WSL". In the bottom left corner you should see this symbol:
![image](https://github.com/Splines/pap1/assets/37160523/04ab6ff8-58b9-4c69-8790-8fc4154e4d3d)

Next, open a terminal in Ubuntu, e.g. either in VSCode directly or with the Windows Terminal. Navigate to a folder where you want to store your code, e.g. for me its `cd ~/programming/`. It could be anything you want. Then, download the repo content via `git clone https://github.com/Splines/pap1.git`. Go to the folder with `cd ./pap1/` (`./` means starting relative to the current directory). Yeah, you've got the code.

Note you can save the current workspace with `File -> Save Workspace as`, then click `Show Local` to see your Windows folders, and store in somehwere, e.g. on your Desktop. This way, you just have to click on the workspace and VSCode starts exactly in the folder you left with the preferences you want for that workspace. I find this really convenient to quickly get back to a project. (You can also add different folders to your workspace by clicking `Add Folder to workspace`, e.g. different `Git` projects.) For example, in my `pap` workspace, I have the `pap1` folder as well as the [`pappe` project](https://github.com/paul019/pappe) added to access them quickly, even though you don't have to be in the same folder on WSL.

![image](https://github.com/Splines/pap1/assets/37160523/fa6842f9-0d3a-43d3-ade2-9727ba2ecc0f)


Go the folder where `document.tex` lives (e.g. `cd ./Versuche/Versuch11/abgabe`). With `ls -hl`, you should see `document.tex` listed there. Change anything in the `content/test.tex` file, then finally compile the PDF:

```
pdflatex ./document.tex
```

(use tab completion! e.g. type `pdflatex ./do`, then press tab)

It should run through without any errors (maybe just some warnings like `found PDF version <1.6>, but at most version <1.5> allowed`, but don't worry about those).
Then, open the PDF document in VSCode and tada you made it 🔥. Change the code again, compile and the PDF should automatically refresh. It shouldn't take any longer than around 2 seconds to compile the PDF with `pdflatex` (this is also called a "binary" that you are executing).

### Use LaTeX Workshop

[`LaTeX Workshop`](https://github.com/James-Yu/LaTeX-Workshop) is a powerful plugin that works well with WSL in VSCode. Install it, then do the following:

The plugins [searches](https://github.com/James-Yu/LaTeX-Workshop/wiki/Compile#multi-file-projects) for the file with `\documentclass{}` in it and assumes this is the root file. We had a slightly different structure. The only thing you need to do is: cut the first line from the `preamble.tex` and insert it before `\input{preamble}` in our root tex file.

Then, tell LaTeX Workshop that we want to build with `pdflatex`. To do so, press `Ctrl + ,` to open the settings tab. Click on `Workspace` to change the workspace settings. On the right top, click on the button thas has "Open Settings (JSON)" as tooltip. Inside the `settings` block, paste this configuration:

```
"latex-workshop.latex.tools": [
			{
				"name": "pdflatex",
				"command": "pdflatex",
				"args": [
					"--shell-escape",
					"-synctex=1",
					"-interaction=nonstopmode",
					"-file-line-error",
					"%DOC%"
				]
			}
		],
		"latex-workshop.latex.recipes": [
			{
				"name": "pdflatex",
				"tools": [
					"pdflatex"
				]
			}
		]
```

Awesome, now open any file of your current document, e.g. `intro.tex`, change something, save the file and it should be automatically compiled. More options are available via the LaTeX button in the left pane of VSCodee. Best thing: PDF and code syncs! E.g. click on a word in the PDF viewer with `Ctrl + Click on word`. The respective file will open at that position. The other way round: Place your cursor in the tex document and press `Ctrl + Alt + J`, it's magic ;)

That's it, for more details consult the [wiki](https://github.com/James-Yu/LaTeX-Workshop/wiki).
