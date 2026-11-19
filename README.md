# TU Delft report (LaTeX)

A report written with the official [TU Delft report template](https://www.overleaf.com/latex/templates/tu-delft-report/hswxghsnfsbw).
It is kept in Git on GitHub, can be edited in Overleaf, and builds locally in VS Code with a live PDF preview.

- [Project structure](#project-structure)
- [Setup, step by step](#setup-step-by-step)
  1. [Install the tools](#1-install-the-tools)
  2. [Configure Git and GitHub](#2-configure-git-and-github)
  3. [Get the project](#3-get-the-project)
  4. [Install TeX Live](#4-install-tex-live)
  5. [Set up VS Code](#5-set-up-vs-code)
  6. [Connect Overleaf](#6-connect-overleaf)
- [Daily workflow](#daily-workflow)
- [Troubleshooting](#troubleshooting)
- [About the template](#about-the-template)

## Project structure

```
report.tex          main file: document settings and the list of chapters
report.bib          bibliography (BibLaTeX / biber)
frontmatter/        cover.tex, title.tex, preface.tex
chapters/           one .tex file per chapter
appendices/         one .tex file per appendix
figures/            images; \includegraphics looks here automatically
-texmf/             the TU Delft class and style files (do not move)
latexmkrc           tells latexmk/Overleaf where -texmf/ is (do not move)
.vscode/            LaTeX Workshop settings for VS Code
```

`report.tex`, `latexmkrc` and `-texmf/` must stay at the top level, or neither Overleaf nor a local build will find the template.

To add a chapter, create `chapters/<name>.tex` (starting with `\chapter{...}`) and add `\include{chapters/<name>}` to `report.tex` after `\mainmatter`.

## Setup, step by step

These steps assume Windows with WSL (Ubuntu). On macOS or Linux, skip the WSL parts.

### 1. Install the tools

1. **WSL with Ubuntu.** In PowerShell run `wsl --install`, restart, and create a Linux user.
2. **VS Code** from <https://code.visualstudio.com>, plus the **WSL** extension (`ms-vscode-remote.remote-wsl`).
3. Accounts on **GitHub** (<https://github.com>) and **Overleaf** (<https://www.overleaf.com>).

### 2. Configure Git and GitHub

In the Ubuntu terminal:

```bash
sudo apt update && sudo apt install git
git config --global user.name  "Your Name"
git config --global user.email "you@example.com"   # the email of your GitHub account
git config --global init.defaultBranch main
```

Create an SSH key and add it to GitHub so you can push without a password:

```bash
ssh-keygen -t ed25519 -C "you@example.com"   # press Enter to accept the defaults
cat ~/.ssh/id_ed25519.pub                     # copy this output
```

On GitHub go to **Settings → SSH and GPG keys → New SSH key**, paste the key and save. Test it with `ssh -T git@github.com`.

### 3. Get the project

**Working on this existing repository:**

```bash
git clone git@github.com:<github-user>/<repo>.git
cd <repo>
code .
```

**Starting a new report from scratch** (how this repository was made):

1. Create an empty repository on GitHub, clone it and open it in VS Code as above.
2. Open the [TU Delft report template](https://www.overleaf.com/latex/templates/tu-delft-report/hswxghsnfsbw) and click **Open as Template**.
3. In the new Overleaf project choose **Menu → Download → Source** to get a `.zip`.
4. Unzip it into the repository folder. You should have `report.tex`, `latexmkrc`, `-texmf/`, the `.tex` chapter files, `report.bib` and the cover images.
5. Optionally sort the files into the folders shown under [Project structure](#project-structure). If you do, update the `\include{...}` lines in `report.tex` and the two cover image paths in `frontmatter/cover.tex`.
6. Add a `.gitignore` for LaTeX build files (`*.aux`, `*.log`, `*.bbl`, `*.toc`, `report.pdf`, ...). The one in this repository can be copied.
7. Commit and push:
   ```bash
   git add .
   git commit -m "Add TU Delft report template"
   git push
   ```

### 4. Install TeX Live

The template is recent and needs a recent TeX Live. Do **not** use `sudo apt install texlive-full`: Ubuntu's version is too old. Install it from the official installer instead (a few GB, takes a while):

```bash
cd /tmp
wget https://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz
tar xzf install-tl-unx.tar.gz
cd install-tl-2*
sudo perl ./install-tl --no-interaction
```

When it finishes it prints the folder it installed to, e.g. `/usr/local/texlive/2026`. Add its `bin` folder to your `PATH` (use your year):

```bash
echo 'export PATH="/usr/local/texlive/2026/bin/x86_64-linux:$PATH"' >> ~/.bashrc
source ~/.bashrc
latexmk --version   # should print a version number
```

Test a build from the project folder:

```bash
latexmk -pdf report.tex
```

This creates `report.pdf`.

### 5. Set up VS Code

1. **Restart VS Code** completely, so it picks up the new `PATH`. With WSL you may need to run `wsl --shutdown` in PowerShell first.
2. Install the **LaTeX Workshop** extension (`James-Yu.latex-workshop`). Make sure it is installed in WSL: the button says *Install in WSL: Ubuntu*.
3. Open `report.tex`. The settings in `.vscode/settings.json` are picked up automatically:
   - the PDF rebuilds every time you save;
   - **Ctrl+Alt+V** opens the PDF preview in a tab next to the source;
   - **Ctrl+Alt+B** builds manually;
   - **Ctrl+click** in the PDF jumps to the source line, and **Ctrl+Alt+J** jumps from the source to the PDF.

If you start a new project, create `.vscode/settings.json` with:

```json
{
  "latex-workshop.latex.tools": [
    {
      "name": "latexmk",
      "command": "latexmk",
      "args": ["-pdf", "-synctex=1", "-interaction=nonstopmode", "-file-line-error", "%DOC%"]
    }
  ],
  "latex-workshop.latex.recipes": [{ "name": "latexmk", "tools": ["latexmk"] }],
  "latex-workshop.latex.rootFile.doNotPrompt": true,
  "latex-workshop.view.pdf.viewer": "tab",
  "latex-workshop.latex.autoBuild.run": "onSave"
}
```

### 6. Connect Overleaf

**With GitHub sync** (needs a paid Overleaf plan, or an institutional licence):

1. In Overleaf: **New Project → Import from GitHub**, authorise GitHub and pick this repository.
2. Overleaf and GitHub are now linked. In Overleaf, **Menu → GitHub** lets you *pull* changes from GitHub and *push* Overleaf edits to GitHub.

**Without a paid plan:** zip the project folder and use **New Project → Upload Project**. This creates a separate copy that does *not* stay in sync with GitHub, so pick one place to edit.

In either case, check in the Overleaf **Menu**:

- **Main document:** `report.tex` (not `cover.tex`, which can also compile on its own).
- **Compiler:** `pdfLaTeX` (faster) or `LuaLaTeX` (better Roboto font support).

## Daily workflow

Locally in VS Code:

```bash
git pull                       # get changes made on Overleaf or by others
# ... write, save, check the preview ...
git add .
git commit -m "Write method chapter"
git push
```

In Overleaf with GitHub sync: **Menu → GitHub → Pull** before you start, and **Push** when you are done.

Avoid editing the same file in Overleaf and VS Code at the same time without syncing in between, to prevent merge conflicts.

## Troubleshooting

- **`latexmk: command not found` in VS Code:** TeX Live is not on the `PATH` VS Code sees. Check step 4, then fully restart VS Code (and `wsl --shutdown`).
- **`File 'tudelft-report.cls' not found`:** `latexmkrc` or `-texmf/` was moved or renamed; both must be at the top level. Build with `latexmk`, not plain `pdflatex`.
- **Bibliography missing or `[?]` citations:** run a full build with `latexmk` (it runs biber). Clean old build files with `latexmk -C` if it gets stuck.
- **Warning `\end occurred when \ifnum ... was incomplete`:** comes from the template's cover and is harmless.

## About the template

The [TU Delft report template](https://gitlab.com/novanext/tudelft-report) follows the [TU Delft corporate design](https://www.tudelft.nl/en/tu-delft-corporate-design). It is based on KOMA-Script and works with pdfLaTeX and LuaLaTeX. It updates the earlier template by [K. P. Hart](https://www.overleaf.com/latex/templates/tud-report/qrntwbrqpckw). The version in this repository is class version 2026/06/01, from upstream commit 796d738.

**Fonts.** By default the template uses Roboto Slab with Arial or Roboto, matching the [TU Delft house style](https://www.tudelft.nl/huisstijl/). For a more classic look, add the class option `fourier` in `report.tex`. With LuaLaTeX you can use Roboto's variable-font features; see [Roboto Flex](https://m3.material.io/blog/roboto-flex).

**Printing.** The class option `print` makes all links black. `\MakeCoverBack` at the end of `report.tex` adds the back cover.

**Latexmk.** Overleaf uses `latexmkrc` to decide how to build the project. It is set up so that it does not override the compiler you choose in Overleaf. See [How Overleaf compiles your project](https://www.overleaf.com/learn/how-to/How_does_Overleaf_compile_my_project%3F).
