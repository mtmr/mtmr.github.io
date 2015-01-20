---
layout: post
title: "Sed - A Microprimer"
date: 2015-01-20 18:07:38 +0530
comments: true
categories: ["linux", "commandline"]
---
~~Note: This is a draft of a much longer post. Work in progress.~~

Hello. This a quick introduction to the Sed utility found in unix-based operating systems. Sed is a contraction of __S__tream __ed__itor. It is a non-interactive text manipulator that manipulates text files, line by line. It is very powerful and can save hours of manual text editing with simple one line commands. This is not a complete guide to Sed. For that, you may want to look at the book *Sed and Awk* published by O'Reilly. Also, sed can handle scripts which can specify mutliple actions to be done. However this primer does not cover that topic and we will restrict ourselves to commands that can fit on one line.
<!-- more -->


The General syntax of a sed command is 

-> ` sed [-n] [-e] [address1[,address2]][!] command [arguments] ` <-

All the options within square brackets are optional. Lets see what the options mean now.

* `-n` - Suppress normal output 

* `-e` - Tells the sed that following argument is a command. This is useful when multiple commands are specified in the same line.

* `[address1[,address2]]` - The address range in the input text on which the follwing commands will be executed. Note that address can be specified as a range or only specify the beginning line (sed assumes that last line is the end address) or omitted entirely. Address can be specified as either line numbers or as search patterns which will result in the address being the line which contains the search pattern.

* `!` - This tells sed to process lines __other__ than the ones specified in the address range.


An example will clear things up..

(You can execute these commands from each line in the terminal)

``` bash Sed Example
echo "John Connor" | sed p
echo "John Connor" | sed -n p
echo -e "John\nConnor\nis\nthe\nleader\nof\nresistance" | sed -n 1,4 p
echo -e "John\nConnor\nis\nthe\nleader\nof\nresistance" | sed -n /John/,/of/p
echo -e "John\nConnor\nis\nthe\nleader\nof\nresistance" | sed -n '/John/,/of/!p'
```

__Output__
``` bash 
1. John Connor

   John Connor

2. John Connor

3. John

   Connor

   is

   the

4. John

   Connor

   is

   the

   leader

   of

5. resistance
```
Most commonly used commands
---------------------------

`p` - Prints input lines when used alone. Prints the input pattern when used along with `s` command. 

`s` - Substitutes the search pattern with a replacement pattern

`c` - Changes the text in the given address-range to a replacement text.

`y` - Replaces the search pattern character by character using the replacement pattern

Command Syntax and Usage Examples
=================================

Substitute 
----------------

The syntax and usage of `p` command has been demonstrated already. Let's see `s` now.

Syntax for `s` command is as follows...

-> `sed [-n] [address1[,address2]][!]s/search pattern/replacement pattern/[p][g]` <-

While it may seem really complex, it is very easy. Here is an example. Outputs are below each command as comments.

``` bash
echo muthu | sed -n 's/muthu/muthuraj/p'
#muthuraj
echo -e "John\nConnor\nis\nthe\nleader\nof\nresistance" | sed -n '/John/,/of/s/John/John J./'
#John J.
echo "John John John John" | sed -n 's/John/Paul/'
#Paul John John John
echo "John John John John" | sed -n 's/John/Paul/g'
#Paul Paul Paul Paul
```
Don't worry if you don't understand these... We will see a primer on regular expressions soon...(I know it should've beeen first. Sorry!!) Watch out for the session announcement..

```bash Substituting using regular expressions
echo "This is a sentence" | sed 's/\(^[A-Za-z][a-z]*\) \([a-z][a-z]*\)/\2 \1/p' # swap first two words
#is This a sentence
echo "This is a sentence" | sed 's/\([A-Za-z][a-z]*\) \([a-z][a-z]*\)/\2 \1/gp' # swap all word pairs
#is This sentence a
```

You can combine address ranges in above commands and have the words swapped over a range of lines or lines that have a certain pattern.

Change
-------------

Syntax for change `c` command is as follows...
 ` sed [address1[,address2]]c\
	replacement text`

``` bash Change in sed
echo -e "John\nConnor\nis\nthe\nleader\nof\nresistance" | sed  '/John/,/of/c\
Changed'
#Changed
#of
```

Note that All of the lines in the given range are changed to the gicen text. You can have multiple lines of replacement text. Also after the `\` you should press `Enter` before entering the replacement text.

`y` Command
----------------

This is somewhat akin to the `tr` command in unix....

Syntax for `y` command is as follows...

-> `sed [address1[,address2]]y/search/replacement/p` <-


``` bash 
echo -e "java" | sed y/abcdefghijklmnopqrstuvwxyz/ABCDEFGHIJKLMNOPQRSTUVWXYZ/
#JAVA
```

Above command changes from lowercase to uppercase.
Characters in search pattern are replaced by corresponding characters in replacement pattern.

We will see more later... Comment if you have feedback/doubts... :)

Many thanks to Loshmitha for proof-reading....
