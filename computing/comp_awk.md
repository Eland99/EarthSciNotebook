# AWK Notes

Awk is great for editing delimited text files, and can be easily used in
[shell scripts](shellscripts).

Back to [general programming page](procedures:programming)

## Basic stuff

Code blocks, which are executed for each line of the text file, are in
curly braces: ~~~ awk '{ print }' AZ\_soilstations.csv

1.  this will print the AZ\_soilstations.csv file to the shell (assuming
    it is in the current working directory).

~~~ Awk can operate each line of the file as a whole, or in fields
separated by a special character: ~~~ awk '{ print \$0 }'
AZ\_soilstations.csv

1.  this prints the whole line

awk '{ print \$1 }' AZ\_soilstations.csv

1.  this prints the line up to the first field separator (default is one
    or more spaces)

~~~ The field separator (FS) can be set prior to running the code
block: ~~~ \$ awk -F"," '{ print \$1 " " \$3 }'
AZ\_soilstations.csv

1.  prints the first and third column of this comma delimited field

~~~ Expressions, such as pattern matching, can be added before the
code block. The following will output the network, station id, state,
and name of all scan sites in the file: ~~~ awk -F"," '/scan/ {
print \$1 " " \$2 " " \$9 " " \$12 }' AZ\_soilstations.csv

1.  search expressions should be surrounded with forward slashes.
2.  outputs: scan 2026 AZ WALNUT GULCH \#1

~~~ Comparison expressions (==, &lt;, &gt;, &lt;=, &gt;=, !=, plus
\~ (matches) and !\~) can also be used: `awk` `-F","` `'$1=="snotel"`
`{` `print` `$1` `"` `"` `$2` `"` `"` `$9` `"` `"` `$12` `}'`
`AZ_soilstations.csv` Outputs:

snotel 310 AZ BALDY
snotel 1121 AZ FORT VALLEY
snotel 969 AZ HAPPY JACK
snotel 1125 AZ MORMON MTN SUMMIT
snotel 861 AZ WHITE HORSE LAKE`

As can logical expressions such as AND (&&) and OR (||): ~~~ awk
-F"," '( \$1 == "snotel" ) && ( \$7 &gt; 7000 ) { print }'
AZ\_soilstations.csv

1.  prints only the lines of AZ snotel sites above 7000 ft elevation

~~~

Other features:
---------------

* Can use arithmetic operators (+-*/%^)
* Can use conditional statements for control flow:
  * if ( //expression// ) //statement1// else //statement2//
  * while ( //expression// ) //statement//
  * for ( //expression1//; //expression// ; //expression2// ) //statement//
  * do //statement// while( //expression// )
* Some important environmental variables:
  * NF (number of columns) 
  * NR (the current line that awk is working on)
  * END (true if awk reaches the EOF)
  * BEGIN (true before awk reads anything)`

Built in functions
------------------

* gsub(r,s)       substitutes s for r globally in current input line, returns the  number of substitutions 
* gsub(r,s,t)     substitutes s for r in t globally, returns number of substitutions 
* index(s,t)      returns position of string t in s, 0 if not present 
* length(s)       returns length of s 
* match(s,r)      returns position in s where r occurs, 0 if not present 
* split(s,a)      splits s into array a on FS, returns number of fields 
* split(s,a,r)    splits s into array a on r, returns number of fields 
* sprintf(fmt, expr-list) returns expr-list formatted according to format string  specified by fmt 
* sub(r,s)        substitutes s for first r in current input line, returns number of substitutions 
* sub(r,s,t)      substitutes s for first r in t, returns number of substitutions 
* substr(s,p)     returns suffix s starting at position p 
* substr(s,p,n)   returns substring of s length n starting at position p `

## Command line usage

 **Note that: \*\*

* Input can come from files or be piped in from shell commands.
* Output can be redirected into files or piped to bash, etc.
* Lots of these are from `[`here`](http://www.catonmat.net/blog/awk-one-liners-explained-part-one/)or `[`here`](http://www.catonmat.net/blog/awk-one-liners-explained-part-two/)\
* Also see examples in the `[`shell` `scripting`
`page`](procedures:shellscripts)`.`

Convert Windows/DOS newlines (CRLF) to Unix newlines (LF) from Unix.
Removes the carriage return (\\r) \*\*at the end\*\* of the line (\$ at
end of search pattern), leaving linefeed: `awk` `'{` `sub(/\r$/,"");`
`print` `}'` `filename.txt` This would remove \*\*one or more\*\* (+ in
pattern) \*\*leading\*\* (\^ in pattern) spaces for each line: `awk`
`'{` `sub(/^` `+/,"");` `print` `}'` `filename.txt` This would remove
one or more leading spaces \*\*or tabs\*\* (brackets join multiple
search terms) in the fifth column only (the \$5 marks column 5):
~~~ awk -F"," ' BEGIN{OFS=","} { gsub(/\^ +/,"", \$5); print }'
AZ\_soilstations.csv

1.  Prints the entire .csv file, spaces are removed from column 5.
2.  Because this is a .csv the field separator must be set (-F)
3.  To preserve comma delimiters on output the output field
    separator (OFS) must be set in a begin block.

~~~ To do the same operation on several fields just add another
statement to the code block: `awk` `-F","` `'` `BEGIN{OFS=","}` `{`
`gsub(/^` `+/,"",` `$5);` `gsub(/^` `+/,"",` `$7);` `print` `}'`
`AZ_soilstations.csv` This command will print a textfile containing
sitenumbers for each of a sites SNOTEL files in a data directory with
filenames like 828\_ALL\_WATERYEAR=2002.csv (all files begin with an
integer site code and there are files for multiple years in the
directory). `ls` `*.csv` `|` `awk` `-F"_"` `'{print` `$1}'` `>`
`sitelist.txt` This nifty bash command moves and renames an entire
directory of files with awk: ~~~ ls junk\* | awk '{print "mv
"\$0" ../trashdir/"\$0".dat"}' | bash

1.  No pattern to match, so for each line of input piped in by ls,
    prints mv junk1 ../trashdir/junk1.dat ....etc to bash

~~~

## Executing saved scripts

BEGIN blocks allow initialization code (such as setting variables)
before running the code block on each line of the input file: ~~~

BEGIN { 
        FS=":" 
} 
{ print $1 }
# Setting the field separator is best done in a BEGIN block before running the code block`

~~~ End blocks do end of script reporting or calculations:
~~~

BEGIN { x=0 } 
/^$/  { x=x+1 } 
END   { print "I found " x " blank lines. :)" }
# Prints out the number of blank lines in the file`

~~~ If saved in a file the script above could be run with:

awk -f myfile.awk AZ_soilstations.csv`
