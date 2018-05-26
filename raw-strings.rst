
Raw string statement
=====
``author: Mikhail V., 2018``

Issue 
======

Vast majority of tasks include operations with text in  
various grades of complexity. Strings are ubiquitous 
and are needed even in simplest scripts, e.g. for  
defining file paths.

Python strings are interpreted - i.e. backslash ``"\"`` may 
change the contents of a string. 
Raw string type ``r""`` has the least amount of such cases, 
still the inclusion of the quote character needs escaping. 

There is no string type which is totally uninterpreted.
As a result, *any* text piece that contains a quote must 
be **edited** before it can be used in sources.
This may seem a minor problem but if we count all 
cases, then the cumulative long-term impact may be 
significant.  
Further, using TQS requires more manipulations 
because it has much more interpreted sequences.

Also this problem may become particularly acute in 
cases related to:

- development of text/code generators 
- using text with important information which is hard 
  to read (e.g. mixed symbols)
- text processing with a lot of literal data definitions
- embedding other language as TQS blocks

Such applications **require** a lot of literal text definitions 
and lack of possibilty to use text directly may even lead 
to frustration by performing of some tasks.
Manipulations with original texts leads to necessity 
for keeping track of changes in these pieces in some 
systematic manner, but this is not possible without 
advanced specialized software. 

Using external resources for these tasks could help, but it 
may lead to even worse experience because of spread 
definitions and increased maintenance times.

The most common solution in existing syntax - triple quoted 
strings has some additional issues: 

- Data is parsed including indents. This may be a benefit for 
  some tasks (e.g. code generators) but it also may become 
  confusing for the reader if it is not aligned within containing 
  block. So-called "de-denting" is also needed. 

- Triple quotes cause visual ambiguity in some edge-cases, 
  e.g. when a string starts or ends with a quote. In many fonts 
  a pair of single quotes is visually identical to one double quote.


Proposal
======

It is suggested to add syntax for the "raw string statement".
This should enable the possibility to define text pieces in 
source code without the need for interpreted characters.
Thereby it should eliminate the need for manipulations 
with these text pieces and this should solve all of the above 
mentioned issues. 

Additionally it should solve some issues with readability 
and data input.


Specification
======

Raw string statement has the following form:

.. code:: python

	variable_name >>> [loader parameter]
	first line of text
	second line of text 
	...

Here "loader parameter" is a string - namely a match 
string for deciding when to stop and return a string 
variable. 

Explanation with an example: 

Say we have a text block with 2 leading spaces. 

To make them into a string simply write:

.. code:: python

	data >>> "  "
	  first line  
	  last line
	#rest of code

The parameter in this case is two spaces in a string ``"    "``.
It will load the following block by simple rule: 

- take ``"  first line"`` and compare its beginning with ``"  "`` string
- if true : the line is loaded
- if not true : stops and returns the string
- repeat this for next lines

In this case the result will be a string: ``"  first line\n  last line"``

	NOTE: the loader should of course be aware of the indent of 
	the containing block. In this case, the line ``data >>> "  "``
	is assumed to be starting level of indentation. So the approach 
	will work independently from the parent indentation.

Further, the syntax suggests a parameter modifer. 
Here the match sequence ``"  "`` is prefixed with ``!``:

.. code:: python

	data >>> !"  "
	  first line  
	  last line
	#rest of code

Here the loading algorithm is same, with only difference that the 
string ``"  "`` will be **removed** from the result, so it returns
a string without these leading spaces:  ``"first line\nlast line"``

Further, one more modifier is suggested: 

Here the match string is prefixed with ``?``.

.. code:: python
	
	data >>> ?"#eof"
	first line  
	last line
	#eof
	
The loading algorithm in this case is different:
it will load each next line *until* the line 
beginning matches ``"#eof"``.

The benefit of such option - the data can be presented 
without additional indentation. It may be also prefered 
due to explicit terminator.

Special flag for blocks that need to start from the line 
beginning (do not honor suite indents): 

.. code:: python

		data >>> X"#tag"
	first line  
	last line
	#tag

It will treat the block "as is", like in current TQS behaviour,
namely will load everything together with indents. The tag
must be in the beginning of the line.

Convenience syntax can be introduced for one-line assignment. 
Although it should have slightly different syntax. 

Suggested syntax:

.. code:: python

	data >>| line of text
	
Here loading starts after the vertical bar, leading space 
is not included, so positioning of string is free. 
String terminates always with the newline. Trailing space 
is removed. This rule is under question though.


Alternative symbols suggestion
-----------------

Alternative spellings for the stream ``>>>`` operator
are suggested:

Triple colon ``:::`` for block definition:

.. code:: python

	data ::: !"  "
	  first line
	  last line
	#

This seems to have a lightweight, less distracting 
look than ``>>>``, at least in some fonts.


Main problems with the proposals
--------
- new syntax requires change to the Python parser
- syntax highlighting may not work properly, for  
  example if the text contains ``"""``, etc.

