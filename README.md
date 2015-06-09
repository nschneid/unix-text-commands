# Unix Text Processing Command Reference

### [Nathan Schneider][nschneid], 2013-01-29

[nschneid]: http://nathan.cl

This is intended as a quick reference for text processing commands built into Unix. It is terse and not necessarily comprehensive—YMMV.

Suggestions? Contact the author or submit a pull request.

Notes about the commands below:

* None of these commands actually modify the input files; rather, they manipulate input text and produce output text, typically writing to standard output.

* `ALLCAPS` indicates a metavariable.

* The descriptions are selective. For more comprehensive documentation of options, see the command’s `man` page. For tutorials and examples, search the Web.


Some tutorials and references:

* Ken Church’s [Unix™ for Poets](http://www.stanford.edu/class/cs124/kwc-unix-for-poets.pdf)
* Jim Notwell’s [Introduction to Text-Processing](http://www.stanford.edu/class/cs273a/presentations/UnixTextProcessingPrimer.pdf)
* Na-Rae Han’s [Command-line Magic](http://www.pitt.edu/~naraehan/commandline-magic.pdf)
* Advanced Bash-Scripting Guide: [Text Processing Commands](http://tldp.org/LDP/abs/html/textproc.html)
* [GNU Coreutils](https://www.gnu.org/software/coreutils/manual/coreutils.html)
  * for Mac OS X: [coreutils](https://code.google.com/p/rudix/wiki/coreutils), [sed](https://code.google.com/p/rudix/wiki/sed) (BSD implementations are built-in on OS X)

More powerful tools for advanced text processing operations:

* [`AWK`](https://en.wikipedia.org/wiki/AWK)
* [`pyp`](https://code.google.com/p/pyp/), grep/sed/AWK for the Python-inclined
  * [Python tips and tricks](http://www.cs.cmu.edu/~nschneid/pythonathan/)


## No input stream

#### [`yes`][]

Repeats a line (by default, `y`) infinitely.

  * `yes LINE | head -n 10` repeats `LINE` 10 times.

## One or more input files/streams

#### [`cat`][]

Concatenate the input files together in sequence.

  - `-s`: suppress/squeeze multiple consecutive blank lines
  - `-n`: number all lines (cf. `nl`)
  - `-b`: number non-blank lines

#### [`zcat`][]

Like `cat`, but for gzipped files.

#### [`tac`][]

Like `cat`, in reverse: lines are printed in reverse order. (GNU but not BSD.)

  * To print the last line of (contiguous) groups sharing all but
    the first 2 fields in common: `tac FILE | uniq -f 2 | tac`

#### [`wc`][]

Counts lines/words/characters in the specified file(s), individually and in total. By default, displays lines, then words, then characters.

  - `-l`: count lines
  - `-w`: count words
  - `-c`: count characters

## Typically a single input stream

### Encoding

#### [`file`][]

Determines the encoding of a text file, or indicates that the argument is a directory or pipe.

#### [`iconv`][]

Converts the encoding of a text file.

  - `iconv -f ISO-8859-1 -t UTF-8 FILE` converts from ISO-8859-1 to UTF-8

### Filtering/extracting by position

#### [`cut`][]

Extracts fields from a file, based on delimiters or character positions.

  -  `cut -f1 FILE` retrieves the first (tab-separated) column
     from the file
  -  `cut -d' ' -f1,3 FILE` retrieves the first and third
     space-separated tokens from each line
  -  `cut -d'`<br/>
     `' -f20-30 FILE` (with a line break) supposedly retrieves the
     20th-30th lines of the file, though this doesn’t seem to work in
     OS X. Equivalently: `head -n 30 | tail -n 20`
  -  `-s` to omit lines without any delimiter

#### [`head`][], [`tail`][]

Extracts a certain amount of text from the beginning or end of a file.

If multiple files are matched by the argument(s), a header indicating the filename will be displayed.

  -  `-n N`: number of lines to retrieve (default: 10)
  -  `-c N`: number of characters (bytes) to retrieve
  -  `tail -n +N`, `tail -c +N`: `N` indicates an offset relative to the beginning of the file; the rest of the file after that offset will be extracted
  -  `head -n -N`, `head -c -N`: offset relative to the end of the file (GNU but not BSD implementation)
  -  `head -n 100 FILE | tail -n 1` retrieves the 100th line of the
     file
  -  `tail -f FILE` monitors the end of the file, writing to stdout
     as the file is appended to

### Filtering/extracting by content

#### [`uniq`][]

Filters out duplicate lines of input.

  -  `-c`: prefix each line with a count
  -  `-i`: case-insensitive
  -  `-f N`: ignore the first `N` (whitespace-separated) fields of each
     line
  -  `-s N`: ignore the first `N` characters of each line
  -  `-w N`: ignore all but the first `N` characters of each line
  -  Note: If some parts of the line are ignored, the kept and
     discarded lines may differ. The first line with a given “key”
     will be the one that is kept.
  -  other options for filtering repeated or non-repeated lines

#### [`grep`][] + friends

Searches text by regular expression.

  -  `-i`: case-insensitive
  -  `-o`: only show the matched part of the line (if multiple matches
     on an input line, these will be on separate output lines)
  -  `-w`: match only whole words
  -  `-l`: list only files in which matches were found
  -  `-r`: recursive
  -  `-n`: include matching lines and line numbers
  -  `-v` (`--invert-match`): filter out matches
  -  `-c` (`--count`): give counts of the matches within each file instead
     of the matches themselves
  -  `-E` or __`egrep`__: extended regex syntax: unescaped `+`, `(`, and `)` serve as operators
  -  `-F` or __`fgrep`__: literal string matching (no regexes)
  -  `-H`: suppress filename when displaying matches
  -  __`zgrep`__ searches zip files
  -  __`bzgrep`__ searches bz files

### Augmenting

#### [`nl`][]

Adds line numbers to a file. (Cf. `cat -n`.) Options control formatting and counting of the line numbers, including:

  - `-v STARTNUM`: initial counter value (default: 1)
  - `-i INCREMENT` (default: 1)
  - `-w WIDTH`: number of characters to be occupied by line numbers (default: 6)
  - `-s SEP`: separator to follow every line number (default: tab)
  - `-b t`: number non-empty lines (default); `-b a`: number all lines; `-b pREGEX`: number lines matching the regular expression pattern `REGEX`

### Reordering

#### [`sort`][]

Sorts the input lines.

  -  **PUNCTUATION/SPECIAL CHARS [MAY BE IGNORED](http://unix.stackexchange.com/questions/4422/is-gnu-coreutils-sort-broken) depending on the
      value of the `LC_COLLATE` environment variable**
  -  `-f`: ignores case
  -  `-n`: “string numerical value”
  -  `-g`: general numeric sort
  -  `-i`: ignores nonprinting characters
  -  `-r`: reverse
  -  `-u`: unique
  -  `-k`: sort key (field offsets)
  -  `-t`: field delimiter

  * To sort by the first column, keeping only the last record for each: `tac FILE | sort -k1,1 -u`

#### [`shuf`][]

Randomly permutes the input lines. (GNU but not BSD)

#### `rev` 

Reverses each line of the file.


### Rearranging (changing spacing)

#### [`expand`][], `unexpand`

Convert tabs to spaces and vice versa.

#### [`fold`][]

Wrap input text so no line is more than a specified number of characters wide.

  -  `-w WIDTH`: wrap lines in a file to be no more than `WIDTH` characters long (default: 80)
  -  `-s`: breaks lines at word spaces

#### `column`

Formats text into columns (by default, based on whitespace delimiters).

### Dividing

#### [`split`][]

`split FILE OUTPREFIX` breaks up the input into smaller
files *by size*.

Output files are named by the specified prefix and some
number of lowercase alphabetic characters (configurable with `-a`;
defaults to 2, i.e. `aa`, `ab`, etc.). In some implementations `-d`
can be provided to request decimal rather than alphabetic suffixes.
One of the following may be provided to determine the splitting
behavior:

  - `-l NUMLINES`: number of lines in each output file (default: 1000)
  - `-b BYTESIZE`: size of each output file; BYTESIZE can even be in
    kilobytes (`10k`) or megabytes (`10m`)

#### [`csplit`][]

Breaks up the input into smaller files _by content_.

Main arguments are the file, followed by one or more patterns indicating
split points. Each pattern may be a line number, a regexp
(optionally with a line offset), or a number of lines followed by
`{REPEATS}` to indicate `REPEATS` blocks of the specified number of
lines. The line matching the pattern begins a new output file.
Output files are numbered with decimal digits.

 - `-f OUTPREFIX` (default: `xx`)
 - `-n NUMDIGITS` (default: 2)

### Replacing

#### [`tr`][]

Translates characters, e.g. lowercasing text in a file or replacing newlines with spaces.

#### [`sed`][]

Text substitution by regular expression matching.

  -  `sed 's/K\.? ?V?\.? ?/K/g' FILE` replaces all matches of the pattern in the file
  -  `sed '/^$/d' FILE` filters out blank lines of the file
  -  Depending on the implementation, sed may or may not not support
     backslash-denoted characters and character classes such as `\t`,
     `\s`, and `[[:space:]]`. (`\t` and `\s` are supported in GNU but 
     not BSD implementations.) Tabs and newlines can be entered as literals.
  -  Unless `-E` is specified, the plus operator and
     parenthesized subexpressions __MUST HAVE BACKSLASH ESCAPES__ when
     using sed/grep: e.g. `\(.*\).\+`


## Two input streams

Call them `FILE1` and `FILE2`, respectively.

#### [`diff`][]

Compare two files line-by-line. (Cf. `diff3` for three files.) Can also compare directories.

  - `-i`: ignore case
  - `-w`: ignore whitespace
  - `-B`: ignore blank lines
  - `-I REGEX`: ignore differences among lines that all match the regular expression pattern `REGEX`
  - `-x REGEX`: exclude files that match the pattern
  - `-r`: recursively compare subdirectories
  - `-s`: report identical files (file comparison only)
  - `-u`: unified diff display format
  - `-y`: side-by-side display (cf. `sdiff`)
  - `--suppress-common-lines`
  
Other options are useful when comparing code, e.g. `-p`, `-F`, `-D`, and `-E`.

#### `sdiff`

Compare files side-by-side, as with `diff -y`. Many of the `diff` options are available; `-s` is short for `--suppress-common-lines`.

#### [`comm`][]

Displays lines common or unique to two SORTED files,
organized into columns according to their commonality.

  -  `-1` to suppress the first column (lines only in `FILE1`)
  -  `-2` to suppress the second column (lines only in `FILE2`)
  -  `-3` to suppress the third column (lines common to both files)
  -  `-i` for case-insensitive comparison
  -  if the files are sorted, `join -t'`<br/>
      `' FILE1 FILE2` is much faster than `comm -1 -2 FILE1 FILE2`

#### [`join`][]

Merges the lines of two **sorted** text files based on
the presence of a common field.

  -  `-t CHAR` to indicate a delimiter (by default, it is any sequence
     of whitespace). For instance, `join -t'`<br/>
     `' FILE1 FILE2` lists all common lines, assuming the two files are sorted.


## Typically two or more input streams

#### [`paste`][]

Combines/merges lines of multiple files.

  - `paste FILE1 FILE2 …` joins corresponding lines with tabs
    (vertically)
  - `paste -s FILE1 FILE2 …` joins horizontally, with
    corresponding lines of the input files displayed as columns
  - `paste -d'\n' FILE1 FILE2 …` interleaves lines of the
    files

## Three input streams

#### `diff3`

Like `diff`, but for three files.


[`cat`]: https://en.wikipedia.org/wiki/Cat_%28Unix%29
[`comm`]: https://en.wikipedia.org/wiki/Comm
[`csplit`]: http://en.wikipedia.org/wiki/Csplit
[`cut`]: https://en.wikipedia.org/wiki/Cut_(Unix)
[`diff`]: https://en.wikipedia.org/wiki/Diff
[`expand`]: https://en.wikipedia.org/wiki/Expand_%28Unix%29
[`file`]: https://en.wikipedia.org/wiki/File_(command)
[`fold`]: https://en.wikipedia.org/wiki/Fold_%28Unix%29
[`grep`]: https://en.wikipedia.org/wiki/Grep
[`head`]: https://en.wikipedia.org/wiki/Head_(Unix)
[`iconv`]: https://en.wikipedia.org/wiki/Iconv
[`join`]: http://en.wikipedia.org/wiki/Join_%28Unix%29
[`nl`]: https://en.wikipedia.org/wiki/Nl_%28Unix%29
[`paste`]: https://en.wikipedia.org/wiki/Paste_%28Unix%29
[`sed`]: https://en.wikipedia.org/wiki/Sed
[`shuf`]: https://en.wikipedia.org/wiki/Shuf
[`sort`]: https://en.wikipedia.org/wiki/Sort_%28Unix%29
[`split`]: https://en.wikipedia.org/wiki/Split_%28Unix%29
[`tac`]: https://en.wikipedia.org/wiki/Tac_%28Unix%29
[`tail`]: https://en.wikipedia.org/wiki/Tail_(Unix)
[`tr`]: https://en.wikipedia.org/wiki/Tr_%28Unix%29
[`uniq`]: https://en.wikipedia.org/wiki/Uniq
[`wc`]: https://en.wikipedia.org/wiki/Wc_(Unix)
[`yes`]: https://en.wikipedia.org/wiki/Yes_%28Unix%29
[`zcat`]: https://en.wikipedia.org/wiki/Zcat
