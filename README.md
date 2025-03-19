# Sphinx-Documentation

```bash

#!/bin/bash  


# Define project variables
PROJECT_NAME="Your Project"  # Put your project name
AUTHOR_NAME="My Author"    # Put your Author names
VERSION=" "           # Put the version
LANGUAGE="en"             # Put the language

# Create Documentation Directories
if [ -d docs ]; then
    rm -rf docs
fi

mkdir docs

cd docs

# Initialize the Sphinx-Project
sphinx-quickstart <<EOT
y
$PROJECT_NAME
$AUTHOR_NAME
$VERSION
$LANGUAGE
EOT

# Navigate to source directory
cd source

# Append additional configurations to conf.py
cat <<EOF >> conf.py
import os
import sys
sys.path.insert(0, os.path.abspath('../../'))  # Adjust the path if needed
autodoc_member_order = "bysource"

extensions = [
    'sphinx.ext.autodoc', 
    'sphinx.ext.napoleon',
    'sphinx.ext.viewcode',
    "sphinx.ext.mathjax"
]
math_renderer = 'mathjax'
latex_engine = "pdflatex"  # Or 'xelatex', 'lualatex'

latex_elements = {
    "papersize": "a4paper",
    'fontpkg': '',
    'classoptions': ',oneside',
    'extraclassoptions': 'openany',
    "pointsize": "12pt",
    "preamble": r"""
		%\renewcommand{\familydefault}{\rmdefault}
        %\usepackage{lmodern}
        \usepackage{amsmath}
        \usepackage{amsfonts}
        \usepackage{amssymb}
        \usepackage{fancyhdr}
        \usepackage{makeidx}  % To create an index
        \usepackage{tocloft}  % To customize Table of Contents

        % Fancy header and footer
        \pagestyle{fancy}
        \fancyhead[L]{\leftmark}
        \fancyhead[R]{\thepage}
        \fancyfoot[C]{}

        % Table of Contents: Formatting (removing subsections)
        \setcounter{tocdepth}{0}  % Limit ToC to sections only (remove subsections)
        \renewcommand{\cftsecfont}{\normalfont\bfseries}  % Bold section titles
        \renewcommand{\cftsecindent}{0em}                 % No indentation for sections
        \setlength{\cftbeforesecskip}{0.5ex}              % Space before section titles

        % Index Configuration (simplified to sections only)
        \makeindex  % Generate index

        % Page layout adjustments
			 \usepackage{geometry}
        \geometry{top=1.5cm, bottom=1.5cm, left=1.5cm, right=1.5cm}

        % Better verbatim environment for code blocks
        %\usepackage{fvextra}
    """
}

latex_use_index = True
latex_domain_indices = False  #true is you want the python module indices
# TEX Configuration
# Enable MathJax for LaTeX equations with dollar signs (\$...\$)
mathjax_config = {
    "tex": {
        "inlineMath": [["\$", "\$"], ["\\\\(", "\\\\)"]],
        "displayMath": [["\$\$", "\$\$"], ["\\\\[", "\\\\]"]]
    }
}

pygments_style = "sphinx"  # Syntax highlighting style
EOF

# Go back to docs directory
cd ..

# Create the rst files
sphinx-apidoc -o source/ ../ --force


cat <<EOF > source/index.rst
.. $PROJECT_NAME documentation master file, created by
   sphinx-quickstart on Sat Feb  1 20:08:52 2025.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.



.. toctree::
   :maxdepth: 2
   :caption: Contents:

   modules
EOF

# Build documentation in multiple formats

make latexpdf
make html
make latex

echo "Documentation successfully generated!"




````
