---
title: Install the LaTeX compiling environment for Sublime Text 4
date: 2022-02-04 23:46:36 +0000
categories: [Technique, LaTeX]
tags: [Sublime Text 4, LaTeX, windows]
---

This post is to summarize the necessary settings to establish the Sublime Text 4 as the LaTeX editor. The following features are materialized: 

- Auto completion of LaTeX keywords;  
- Equation, figure & citation preview;  
- Inverse search from the compiled PDF file;  
- BibLaTeX integrated in a single compile command.  

Setup on Jan. 10, 2022, with Windows 10 21H2. LaTeX compiler: MikTex 21.12

P.S. I still recommend [Overleaf](https://www.overleaf.com/) as a more compatible choice, as long as the template is supported. 

# List of required packages/software

- [**MikTeX**](https://miktex.org/download) - LaTeX compiler ([TeXStudio](https://texstudio.sourceforge.net/) is compatible as well, modify the settings according to the documentation);  
- [**LaTeXTools**](https://latextools.readthedocs.io/en/latest/) - LaTeX editor plugin for sublime text;  
- [**LaTeX Word count**](https://packagecontrol.io/packages/LaTeX%20Word%20Count) - Explicite word counter for LaTeX documents;  
- [**SumatraPDF**](https://www.sumatrapdfreader.org/free-pdf-reader) - A free and light weight PDF reader. The only PDF reader supporting reverse lookup on Windows platform;  
- [**Ghostscript**](https://www.ghostscript.com/) - Equation preview and image preview for common formats (PNG, JPEG, GIF, PDF, EPS, PS);  
- [**ImageMagick**](https://imagemagick.org/index.php) - Image preview for other formats.  

Sublime Text 4 packages are installed via Package Control. 

# Setup LaTeXTools
## Link the compiler

Preliminary step, not required. The directory of MikTeX executable file should be added to the global environment variable `$Path`. Search 'environmental variable' to modify the value of `$Path`. 

Open 'Preferences/Package Settings/LaTeXTools/Settings - User', Search `texpath` and edit the path according to OSs. For Windows users, if the prerequisite is not satisfied, variables `texpath` and `sumatra` should be specified as the directory of MiKTeX and the executable file of SumatraPDF. Otherwise the variable `texpath` can be left blank. The format of `texpath` should follow the commented line. 

``` json
"windows": {
    "texpath" : "disc:\\miktex\\executable\\folder;$PATH",
    "sumatra": "disc:\\SubatraPDF\\executable\\file\\SumatraPDF.exe"
}
```

## Set auto generation for bibliography

Search the same file for `builder`, modify its value to "script". Then go a little down to find the variable `builder_settings`, set the OS-specific commands. In Windows system, the iterative scheme of 'pdfLaTeX - BibTeX' is adopted: 

``` json
"windows" : {
	"script_commands":[
		"pdflatex -synctex=1 -interaction=nonstopmode",
		"bibtex",
		"pdflatex -synctex=1 -interaction=nonstopmode",
		"pdflatex -synctex=1 -interaction=nonstopmode"
	]
}
```

## Figure & equation previews

**Ghostscript**

LaTeXTools can automatically install the Ghostscript for MikTeX, as long as the directory of its executable file is added as the value of either `texpath` or environmental variable `$Path`. To manually install and specify Ghostscript, add the folder of executable to `$Path` or `texpath`: 

``` json
"texpath" : "disc:\\miktex\\executable\\folder;disc:\\ghostscrip\\executable\\folder;$PATH"
```

For TeXLive users, refer to the [documentation](https://latextools.readthedocs.io/en/latest/install/#windows), and modify environmental variables similarly. 

For ImageMagick, manual installation is required. Then set environmental variables similarly. 

## Check the environment

After finishing all necessary settings, an automatic system check might be helpful to ensure the settings above function correctly. Click on 'Preferences/Package Settings/LaTeXTools/Check System' to run automatic check. Information of installed distributions is also available in the output. 

## Overcome the incompatibility with Sublime Text 4

[An issue](https://github.com/SublimeText/LaTeXTools/issues/1506) is identified with LaTeXTools on Sublime Text 4 which is probably due to incompatibility: the backslash '\\' goes missing when using auto completion. The solution is from the same page, WeixuanZ's comment. 

Click 'Preferences/Browse Packages...' to open the folder 'LaTeXTools/'. Find the script 'latex_cwl_completions.py'. Comment line 308 ~ 312. In previously commented lines the flaw of ST3 in recognizing backslash is explained, which might be fixed in ST4. 

``` python
            # autocompleting with slash already on line
            # this is necessary to work around a short-coming in ST where having a
            # keyed entry appears to interfere with it recognising that there is a
            # \ already on the line
            #
            # NB this may not work if there are other punctuation marks in the
            # completion
            if is_prefixed:
                completions = [
                    (c[0], c[1][1:]) if c[1].startswith("\\") else c
                    for c in completions
                ]
```

# Set SumatraPDF for reverse lookup
In the previous section, SumatraPDF has been set as the default PDF viewer for the output of LaTeX. Generating test file is recommended before taking the following steps. 

After finishing compilation, SumatraPDF should be automatically launched to display the generated file. Meanwhile, in the same directory, there is a '.synctex.gz' file storing the synchronization information. SumatraPDF will show the panel for setting up reverse search only if a document with synchronization data is opened - use an old document also works. 

Open the menu on the top left and click on 'Settings/Options'. In the 'Set inverse search command-line' panel, enter the command below: 

``` console
"disc:\\sublime\\text\\executable\\sublime_text.exe" "%f:%l"
```

Then inverse search is enabled when user double clicks somewhere in the generated PDF file. 

