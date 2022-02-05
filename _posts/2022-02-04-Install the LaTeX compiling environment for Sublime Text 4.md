---
title: Install the LaTeX compiling environment for Sublime Text 4
date: 2022-02-04 23:46:36 +0000
categories: [Technique, Others]
tags: [Sublime Text 4, LaTeX, windows]
---

This post is to summarize the necessary settings to establish the Sublime Text 4 as the LaTeX editor. The following features are materialized: 

- Auto completion of LaTeX keywords;  
- Equation, figure & citation preview;  
- Reverse lookup from the compiled PDF file;  
- BibLaTeX integrated in a single compile command.  

Setup on Jan. 10, 2022, with Windows 10 21H2. LaTeX compiler: MikTex 21.12

# List of required packages/software

- [**MikTeX**](https://miktex.org/download) - LaTeX compiler ([TeXStudio](http://texstudio.sourceforge.net/) is compatible as well, modify the settings according to the documentation);  
- [**LaTeXTools**](https://latextools.readthedocs.io/en/latest/) - LaTeX editor plugin for sublime text; 
- [**SumatraPDF**](https://www.sumatrapdfreader.org/free-pdf-reader) - A free and light weight PDF reader. The only PDF reader supporting reverse lookup on Windows platform;  
- [**Ghostscript**](https://www.ghostscript.com/) - Equation preview and image preview for common formats (PNG, JPEG, GIF, PDF, EPS, PS);  
- [**ImageMagick**](https://imagemagick.org/index.php) - Image preview for other formats.  


