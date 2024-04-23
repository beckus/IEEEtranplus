# IEEEtranplus

## A verbose LaTeX Bibliography Style (bst) with local file links for collecting and organizing references

This tool is an extension of `IEEEtran.bst` (https://www.michaelshell.org/tex/ieeetran/) with some additional features.
I use it as part of my private bibliography: an informal Latex document I built to collect and organize frequently-used references.
It is not intended for use in publications.

For lack of a better name, I call this tool "IEEEtranplus".

## Key features

1. Supports a `file` field in BibTeX entries which links to one or more documents on your local computer.
   This *should* be compatible with the `file` field used by [JabRef](https://www.jabref.org/).

2. A verbose output of additional fields which are useful when collecting/organizing a repository of references:
    - **url**:     A url to the publisher webpage (or other relevant url).
    - **docurl**:  An alternate url that links directly to the document. For example, you could link to a copy of the document
                   available online or through a library.  
    - **library**: A free-form text field to list library call number, or other relevant info.
    - **citekey**: Displays the citation key for the entry. This makes it easy to copy and paste when you want to cite.
    - **note**:    A free form text field for storing notes regarding the reference.

3. Provides a LaTeX script `IEEEtranplus.tex` with useful features:
    - Includes the `hyperref` package, which is needed by `IEEEtranplus.bst`.
    - Include `backref` package to indicate where in the document a reference is cited. This is useful for jumping
      back and forth between the document and references.
    - Various fixes for searching for text within a PDF document (to handle ligatures and underscores).

## Example
Example BibTeX entry:
```
@book{PrincipiaMathematica:1927,
      author    = "Whitehead, Alfred North and Russell, Bertrand Arthur William",
      title     = "{Principia mathematica; 2nd ed.}",
      publisher = "Cambridge Univ. Press",
      year      = "1927",
      url       = "https://www.cambridge.org/us/universitypress/subjects/mathematics/logic-categories-and-sets/principia-mathematica?format=WX&isbn=9780521067911",
      docurl    = "https://cds.cern.ch/record/268025",
      file      = "Volume 1:PrincipiaMathematicaVol1.pdf;Volume 2:PrincipiaMathematicaVol2.pdf;Volume 3:PrincipiaMathematicaVol3.pdf",
      library   = "Library of Congress: QA9.W5 1925",
      note      = "The book can also be viewed online here: \href{https://www.loc.gov/item/25015133/}{Library of Congress}",
}
```

Produces the following output:
```
[1] A. N. Whitehead and B. A. W. Russell, Principia mathematica; 2nd ed. Cambridge Univ. Press, 1927
    URL: https://www.cambridge.org/us/universitypress/subjects/mathematics/logic-categories-and-sets/principia-mathematica?format=WX&isbn=9780521067911
    DOCURL: https://cds.cern.ch/record/268025
    FILE: PrincipiaMathematicaVol1.pdf Volume 1
    FILE: PrincipiaMathematicaVol2.pdf Volume 2
    FILE: PrincipiaMathematicaVol3.pdf Volume 3
    LIBRARY: Library of Congress: QA9.W5 1925
    NOTE: The book can also be viewed online here: Library of Congress
    CITEKEY: PrincipiaMathematica:1927
    CITATIONS: page 1
```

Note that each field is displayed on its own line. Though this format is very verbose and can significantly extend the length
of the document, I find it useful for my private bibliography to have all the info displayed in one place for easy browsing.

Full example documents are given in the [examples folder](https://github.com/beckus/ieeetranplus/tree/main/examples).



## Usage
- Optionally, include the `IEEEtranplus.tex` script in your document header to provide useful features.
  Use the command: `\input{IEEEtranplus}`

- Make sure to include package `hyperref` for links to work properly (this is already included in `IEEEtranplus.tex`).
  Use the command: `\usepackage{hyperref}` in your header.

- Call out the `IEEEtranplus.bst` style in your document using the command: `\bibliographystyle{IEEEtranplus}`

- The referenced files should be put in a `ref` subfolder under the folder where your compiled PDF is located
  (this folder is configurable by editing the `IEEEplanplus.bst` file's `file.folder` function). Then, depending on the
  PDF viewer you are using, you should be able to click on the file name in the PDF and the file will open. This
  works in some PDF viewers such as Adobe Acrobat (tested on Windows). Many PDF viewers allow you to hold the
  "ctrl" key when clicking on the file to open it in a new tab/window.

- The format of the file field is as follows:
    - For a single file: `FILE = "description:file"` or `FILE = {description:file}`
      (as with other fields, you can use double quotes or curly braces).
    - The description is optional, i.e., you could just call out `FILE = "file"`
    - For multiple files, separate with a semicolon. For example:`FILE = "description1:file1;description2:file2"`
    - A file type can be provided. For example `FILE = description:file.pdf:PDF`.
      This type is only included for compatibility with JabRef, and `IEEEtranplus.bst` does not use this type in any way.

- There are various option flags in the "Options for IEEEtranplus" section of `IEEEtranplus.bst`, which can be enabled/disabled.
 


## Technical Notes
- The function `escapechar` performs escaping of special characters.
This function might need to be updated to support additional special characters in the future.

- The `file` field can handle HTML unicode entities (like `&#44;`) in the file name.
  However, there is an issue with the hash tag `#` when used in a link within the `\href` command, even if it is escaped as `\#`.
  I am not sure how to resolve this. Even if this is resolved, e.g. using the `\url` command, it seems there are still
  issues with the PDF reader properly handling this case, so it is best to avoid using them anyway.
  Maybe this could be resolved by changing the category code of the # character.

- The parsing logic for the `file` field is based on the code/algorithm found in the
  [JabRef](https://www.jabref.org/) source code (version 4.3.1) inside the following files:
    - org/jabref/model/entry/FileFieldParser.java
    - org/jabref/model/entry/FileFieldWriter.java

- To include citation keys in the references, you could also use the `showkeys` package. The main reason I included this
  functionality directly in `IEEEtranplus.bst` is to show the key in-line with the text. Though `showkeys` is a great package,
  I had truncation/overlap issues with long keys or when in two column mode 

- Debugging tips:
    - Some useful debugging commands for BST files:
        - Print debug message in log: `"message" top$`
        - Show element on top of stack: `duplicate$ top$`

    - In MikTex, to see the `.bbl` file, perform separate compiles using PdfLatex first, and then BibText second.
      The `.bbl` should then be present in the working folder. Running PdfLatex again will make the final PDF from this
      `.bbl` file (you can edit the bbl and rerun PdfLatex during debugging).

- The BibLaTeX package documentation (http://mirror.hmc.edu/ctan/macros/latex/contrib/biblatex/doc/biblatex.pdf) was used
  as a reference when writing this tool. Note that the documentation does call out a `library` field, though I have never
  seen it used elsewhere.

- The file `IEEEtranplus.bst` is a Derived Work (as defined under the LaTeX Project Public License) written by Andre Beckus.
  The Original Work is as follows:
    - bibtex/IEEEtran.bst  Version 1.14 (2015/08/26)
    - Copyright (c) 2003-2015 Michael Shell
    - Available from:
        - https://www.michaelshell.org/tex/ieeetran/
        - https://www.ctan.org/pkg/ieeetran
    - For a diff showing my modifications, open [this compare in Github](https://github.com/beckus/ieeetranplus/compare/original...main)
      and view the `IEEEtranplus.bst` file.



## Files

- `README.md`:         This file.
- `IEEEtranplus.bst`:  The main LaTeX Bibliography Style. Provides the core functionality of this tool.
- `IEEEtranplus.tex`:  An optional LaTeX script including additional functionality.
- `examples/simple`:   A simple example document.
- `examples/detailed`: A detailed example showing various aspects of the functionality.

## Legal Notice
This code is offered as-is without any warranty either expressed or
implied; without even the implied warranty of MERCHANTABILITY or
FITNESS FOR A PARTICULAR PURPOSE! 
User assumes all risk.
In no event shall the IEEE or any contributor to this code be liable for
any damages or losses, including, but not limited to, incidental,
consequential, or any other damages, resulting from the use or misuse
of any information contained here.

All comments are the opinions of their respective authors and are not
necessarily endorsed by the IEEE.

This work is distributed under the LaTeX Project Public License (LPPL)
( http://www.latex-project.org/ ) version 1.3, and may be freely used,
distributed and modified. A copy of the LPPL, version 1.3, is included
in the base LaTeX documentation of all distributions of LaTeX released
2003/12/01 or later.
Retain all contribution notices and credits.


