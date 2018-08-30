---
layout: post
title: "Vim Commands frequently used"
date: 2013-03-21T00:00:00+08:00
tags: vim tools devops
---
### Using Spell check in vim ###

{% highlight bash %}
# start the spell check
:set spell
# move forward to next misspelled word
]s 
# move backword to next misspelled word
[s
# suggest alternatives
z=
# add word to dictionary
zg
# mark words as incorrect
zw
{% endhighlight %}




### Commenting/Uncommenting multiple lines or vertical column text selection ###
To comment out blocks in vim:

+ hit ctrl+v (visual block mode)
+ use the down arrow keys to select the lines you want (it won't highlight everything)
+ Shift+i (capital I)
+ insert the text you want, i.e. '% '
+ Press ESC
+ Give it a second to work
+ Put your cursor on the first # character, press CtrlV (or CtrlQ for gVim), and go down until the last commented line and press x, that will delete all the # characters vertically.


### Non greedy match in vim ### 
Eg: for matching the first word in lines :/^.\{-}[^ ]\s

- Instead of .* use .\{-}.
- %s/style=".\{-}"//g
- Also, see :help non-greedy

### Using grep in vim ###
Download the plugin from here 
http://www.vim.org/scripts/script.php?script_id=2572

{% highlight bash %}
sudo ln -s /usr/bin/ack-grep /usr/local/bin/ack
{% endhighlight %}

### Using Register ###

{% highlight bash %}
"kyy #(also append to register using "Kyy)
"kp #(to paste it)
{% endhighlight %}

### Using macro ###

{% highlight bash %}
q<letter><commands>q
<number>@<letter>
q #start recording to register q 
#... your complex series of commands
 q #stop recording 
@q #execute your macro 
@@ #execute your macro again
{% endhighlight %}

### Set screen session name ###
{% highlight bash %}
CNTRL + A +:
sessionname <name>
{% endhighlight %}
 
### Delete empty lines ###
{% highlight bash %}
:g/^$/d
{% endhighlight %}

### Delete duplicate lines ###
{% highlight bash %}
:sort u
{% endhighlight %}

### Bash command line ###
C-x C-e to open the line in vim editor

### Window Resizing ###
Cntrl - w + /- 

### Folding ###
{% highlight bash %}
zf #create fold
zf'a # fold to mark
#use zo and then zc to open and close folds
#To move between folds quickly, use the zj and zk
#If you want to move between the start and end of a fold, use [z and ]z
zR #will open all of the folds in the file
zd #to delete fold
#on search vim opens the fold. zm will restore the folds
{% endhighlight %}


### Moving Around ###

{% highlight bash %}
k #(up),
h #(left) 
l #(right).
j #(down)
#(Ex 5k,j,3l )
#Word Movement
w #(Forward Word Movement Ex. f,5f )
b #(Backword Word Movement Ex. b,2b)
e #(Forward Word Movement at the end of word Ex. e,3e )
ge #(Backword Word Movement at the end of word Ex. ge,3ge )
#Moving to the Start or End of a Line
$ #(End Of Line Ex. $,2$)
^ #(Start Of Line Ex. ^,3^)
#Searching Along a Single Line
f<Charcter> #(Searching Charcter Forward Ex. fa, 2fv)
F<Charcter> #(Searching Charcter Backword Ex. Fg,2Ft)
t<Charcter> #(Search till Forward )
T<Charcter> #(Search till Backword)
#Moving to a Specific Line
<Line No.>G #(Go to Line No.)
CTRL-G #(Where I am in the File)
nG #go to line number n
G  #go to end of file
CTRL-O #Jump to previous location.
<TAB> #Jump to next location (line 10).
#Where are you in File
:set number
:set nonumber
{% endhighlight %}

###Scrolling Up and Down###

{% highlight bash %}
#Fast Scrolling (Back - Front)
CTRL-B #(scrolls up a entire screen at a time.)
CTRL-F #(scrolls down one screen of text.)

#Medium Scrolling (Up - Down)
CTRL-U #(scrolls up half a screen of text.)
CTRL-D #(scrolls down half a screen of text.)

CTRL-Y #(scrolls up a line of text.)
CTRL-E #(scrolls down one line.)

z<Enter> #(screen line on the top)
88z<Enter> #positions line 88 at the top.
zt #(Leaves the cursor where it is.)
z- #(scrolls line to the end of the screen)
zb #(Leaves the cursor where it is)
z. #(Center of the screen)
zz #(Leaves the cursor where it is .)
:set scroll=10
:set scrolljump=5
:set scrolloff=3

{% endhighlight %}

### Deleting ###
{% highlight bash %}
x #delete character under the cursor (short for "dl")
X #delete character before the cursor (short for "dh")
dw #(Delete Word Ex. dw,3dw,d3w,3d2w,d$,d^,df> )
dd #(Delete Line Ex. dd,3dd)
D #(Delete up to end of line. )(short for "d$")
diw #delete word under the cursor (excluding white space)
daw #delete word under the cursor (including white space)
dG #delete until the end of the file
dgg #delete until the start of the file

{% endhighlight %}

In general, d<motion> will delete from current position to ending position after <motion>. This means that:

{% highlight bash %}
d<leftArrow> #will delete current and left character
d3<leftArrow> #will delete 3 characters
d3<rightArrow> #will delete 3 characters to right
bdw #will start at beginning of word and delete the word
bd3<rightArrow> #will take to the beginning of the word and delete 3 letters to the right
ed3<leftArrow> #will take to the end of the word delete 3 letter to the left
3bdw #will move 3 words to the left and delete  the word
3ebdw #will move 3 words to the right and delete the word
3ed3<leftArrow> #will move the cursor to 3 words ahead (at the end) and then delete 3 characters on the left
d$ #will delete from current position to end of line
d^ #will delete from current backward to first non-white-space character
d0 #will delete from current backward to beginning of line
dw #deletes current to end of current word (including trailing space)
db #deletes current to beginning of current word

{% endhighlight %}

### Arithemetic ###
{% highlight bash %}
CTRL-A #Incrmenting Number (123, 0177, 0x1f,-98)
CTRL-X #Decrementing Number
:set nrformats=""
{% endhighlight %}

### Changing Text ###
{% highlight bash %}
cw #(Change Word Ex cw,c2w)
C #stands for c$ (change to end of the line)
s #stands for cl (change one character)
S #stands for cc (change a whole line)
{% endhighlight %}

### The . Command ###
It repeats the last delete or change command.

### Joining Lines ###
{% highlight bash %}
J i#(Join Lines to One. Ex J,3J)
gJ #(Join Lines without Spaces)
{% endhighlight %}

### Replacing Character ###
{% highlight bash %}
r<Charcter> #(Replace Charater Under Cursour. Ex. ru,5ra,3r<Enter> )
R<Charcter>
{% endhighlight %}

### Changing Case ###
{% highlight bash %}
~ #(Change Case of Character Ex. ~,12~,~fq)
U #(Make the text Uppercase)
u #(Make the text Lowercase)
g~motion #(It does not depend on tildeop)
g~~ or g~g~ #(Changes case of whole line)
gUmotion #(All uppercase)
gUU #(Changes to uppercase for whole line)
gUw #(Changes to uppercase for word)
guw #(Changes to lowercase for word)
{% endhighlight %}

