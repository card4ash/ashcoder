Read the Docs 
=====================================


Install the Prerequisites
*************************************

Install python and then Install Sphinx


.. code-block :: shell

    pip install sphinx

Install Read the docs theme

.. code-block :: shell

    pip install sphinx_rtd_theme

Use the CLI to Create a Documentation Project
***********************************************

Make a directory and cd into the directory

.. code-block :: shell

    mkdir DocsGS
    cd DocsGS

Run the command

.. code-block :: shell

    sphinx-quickstart

put Project Name, Author Name, leave all default

Open the project with VSCode. Install Restructure Text Extention.Build the project from vs command terminal. 

.. code-block :: shell
 
    make html

browse _build/html/index.html page.

Apply theme
*************

open conf.py file. Edit 

.. code-block :: python
    
    html_theme = 'sphinx_rtd_theme'


**Referance**

 #. https://thomas-cokelaer.info/tutorials/sphinx/rest_syntax.html