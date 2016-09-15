---
layout: default
title:  'Advanced Linux Usage - Variables and Loops'
---

# Advanced Linux Usage - Variables and Loops
**NOTE:** in syntax examples, the dollar sign ($) is not to be printed. The dollar sign is usually an indicator that the text following it should be typed in a terminal window.

## 1. Connecting to UPPMAX
The first step of this lab is to open a ssh connection to UPPMAX. You will need a ssh program to do this:

On Linux: it is included by default, named **Terminal**.

On OSX: it is included by default, named **Terminal**.

On Windows: [Google MobaXterm](http://bit.ly/19yaQOM) and download it.

Fire up the available ssh program and enter the following (replace **username** with your uppmax user name). -Y means that X-forwarding is activated on the connection, which means graphical data can be transmitted if a program requests it, i.e. programs can use a graphical user interface (GUI) if they want to.

```bash
$ ssh -Y username@milou.uppmax.uu.se
```

and give your password when prompted. As you type, nothing will show on screen. No stars, no dots. It is supposed to be that way. Just type the password and press enter, it will be fine.

Now your screen should look something like this:

![](files/uppmax-intro/just-logged-in.jpg)

## 2. Getting a node of your own (only if you canceled your job before lunch)
Usually you would do most of the work in this lab directly on one of the login nodes at uppmax, but we have arranged for you to have one core each to avoid disturbances. This was covered briefly in the lecture notes.

<font color='red'>Check with squeue -u username if you still have your reservation since before lunch running. If it is running, skip this step and connect to that reservation.</font>

(We only have 20 reserved cores, so if someone has two, someone else will not get one..)

```bash
# ONLY IF YOU DON'T ALREADY HAVE AN ACTIVE ALLOCATION SINCE BEFORE
$ salloc -A g2016017 -t 04:30:00 -p core -n 1 --no-shell --reservation=g2016017_monday &
```

check which node you got (replace **username** with your uppmax user name)

```bash
$ squeue -u username
```

should look something like this

![](files/uppmax-intro/allocation.png)

where **q34** is the name of the node I got (yours will probably be different). Note the numbers in the Time column. They show for how long the job has been running. When it reaches the time limit you requested (4.5 hours in this case) the session will shut down, and you will lose all unsaved data. Connect to this node from within uppmax.

```bash
$ ssh -Y q34
```

**Note:** there is a uppmax specific tool called jobinfo that supplies the same kind of information as squeue that you can use as well (```$ jobinfo -u username```).




## 3. Copying files needed for this laboratory
To be able to do parts of this lab, you will need some files.
To avoid all the course participants editing the same file all at once, undoing each other's edits, each participant will get their own copy of the needed files.
The files are located in the folder `/sw/courses/ngsintro/loops/data`

Next, copy the lab files from this folder.
-r means recursively, which means all the files including sub-folders of the source folder.
Without it, only files directly in the source folder would be copied, NOT sub-folders and files in sub-folders.

**NOTE: Remember to tab-complete to avoid typos and too much writing.**

Ex.

```bash
$ cp -r <source> <destination>

$ cp -r /sw/courses/ngsintro/loops/data ~/glob/ngs-intro/loops
```

Have a look in `~/glob/ngs-intro/loops`:

```bash
$ cd ~/glob/ngs-intro/loops

$ ll
```
If you see files, the copying was successful.


## 4. Using variables
Variables are like small notes you write stuff on.
If you want to save the value of something to be able to use it later, variables is the way to go.
Let's try assigning values to some variables and see how we use them.

```bash
$ a=5
```

Now the values 5 is stored in the variable named `a`.
To use the variabe we have to put a $ sign in front of it so that bash knows we are referring to the variable `a` and not just typing the letter a.
To see the result of using the variable, we can use the program `echo`, which prints whatever you give it to the terminal:

```bash
$ echo print this text to the terminal
$ echo "you can use quotes if you want to"
```

As you see, all the words you give it are printed just the way they are. 
Try putting the variable somewhere in the text:

```bash
$ echo Most international flights leave from terminal $a at Arlanda airport
```

Bash will see that you have a variable there and will replace the variable name with the value the variable have before sending the text to echo.
If you change the value of `a` and run the exact command again you will see that it changes.

```bash
$ a="five"
$ echo Most international flights leave from terminal $a at Arlanda airport
```

So without changing anything in the echo statement, we can make it output different things, all depending on the value of the variable `a`.
This is one of the main points of using variables, that you don't have to change the code or script but you can still make it behave differently depending on the values of the variables.

You can also do mathematics with variables, but we have to tell bash that we want to do calculations first.
We do this by wrapping the calculations inside a dollar sign (telling bash it's a vaiable) and double parantasese, i.e. $((5+5))

```bash
$ a=4
$ echo $a squared is $(($a*$a))
```

**Exercise:** Write a echo command that will print out the volume of a [rectangular cuboid](https://www.mathsisfun.com/cuboid.html), with the side lenghts specified by variables named `x`, `y`, and `z`. To see that it works correctly, the volume of a rectangular cuboid with sides 5,5,5 is 125, and for 4,5,10 is 200.

If you get stuck, the solution will be below here in white text.

<font color='white'>
$ x=4<br>
$ y=5<br>
$ z=10<br>
$ echo The volume of the rectangular cuboid with the sides $x,$y,$z is $(($x*$y*$z)).<br>
</font>  

## 5. Looping over lists
First off, let's open another terminal to uppmax so that you have 2 of them open. 
Scripting is a lot easier if you have one terminal on the command line, ready to run commands and test things, and another one with a text editor where you write the actual code. 
That way you will never have to close down the text editor when you want to run the script you are writing on, and then open it up again when you want to continue editing the code. 
So open a new terminal window, connect it to uppmax and then connect it to the node you have booked.
Make sure both terminals are in the `~/glob/ngs-intro/loops` directory, and start editing a new file with nano where you write your script.
Name the file whatever you want, but in the examples I will refer to it as `loop_01.sh`.
Write your loops to this file and test run it in the other terminal.

The most simple loops are the ones that loop over a predefined list. 
You saw examples of this in the lecture slides, for example:

```bash
for i in "Print these words" one by one;
do
  echo $i
done
```

which will print the value of `$i` in each iteration of the loop. 
Write this loop in the file you are editing with nano, save the file, and then run it in the other terminal you have open.

```bash
$ bash loop_01.sh
```

As you see, the words inside the quotation marks are treated as a single unit, unlike the words after. You can also iterate over numbers, so erase the previous loop you wrote and try this instead:

```bash
for number in 1 2 3;
do
  echo $number
done
```

If everything worked correctly you should have botten the number 1 2 3 printed to the screen.
As you might guess, this way of writing the list of numbers to iterate over will not be usable once you have more than 10 or so numbers you want to loop over.
Fortunately, the creators of bash (and most other computer languages) saw this probelm coming a mile away and did something about it.
To quickly create a list of numbers in bash, you can use something called a sequence expression to create the list for you.
 
```bash
for whatevernameyouwant in {12..72};  
do  
  echo $whatevernameyouwant  
done  
```
**Exercise 1**
Now that we know how to use variables and create loops, it's time we start doing something with them.
Let's say it's New Year's Eve and you want to impress your friends with a computerized countdown of the last 10 seconds of the year (haven't we all?).
Start off with getting a loop to count down from 10 to 0 first.

Notice how fast the computer counts?
That won't do if it's seconds we want to be counting down.
Try looking the man page for the `sleep` command (`man sleep`) and figure out how to use it.
The point of using `sleep` is to tell the computer to wait for 1 second after printing the number, instead of rushing to the next iteration in the loop directly.
Try to implement this on your own.
If you get stuck, the solution will be below here in white text.

<font color='white'>
for secondsToGo in {10..0};<br>
do<br>
&nbsp;&nbsp;echo $secondsToGo<br>
&nbsp;&nbsp;sleep 1<br>
done<br>
</font>

**Exercise 2**
Maths and programming are usually a very good combination, so many of the examples of programming you'll see involve some kind of maths.
Now we will write a loop that will calculate the factorial of a number.
As [wikipedia will tell you](https://en.wikipedia.org/wiki/Factorial), "the factorial of a non-negative integer n, denoted by n!, is the product of all positive integers less than or equal to n", i.e. multiply all the integers, starting from 1, leading up to and including a number with each other.
Doing this by hand would start taking its time even after a couple of steps, but since we know how to loop that should not be a problem anymore.
Write a loop that will calculate the factorial of a given number stored in the variable `$n`.

A problem you will encounter is that the sequence expression, {1..10}, from before doesn't handle varaibles.
This is because of the way bash is built.
The sequence expressions are handled before handling the variables so when bash tries to generate the sequence, the variable names have not yet been replaced with the values they contain.
This leads to bash trying to create a sequence from 1 to $n, which of course doesn't mean anything.

To get around this we can use a different way of generating sequences (there are **always** alternatives).
There is a program called `seq` that does pretty much the same thing as the sequence expression, and since it is a program it will be executed **after** the variables have been handled.
It's as easy to use as the sequence expressions; instead of writing {1..10} just write `seq 1 10`.

If you get stuck, the solution will be below here in white text.

<font color='white'>
n=20
factorial=1
for i in $(seq 1 $n);<br>
do<br>
&nbsp;&nbsp;factorial=$factorial*$n<br>
done<br>
echo The factorial of $n is $factorial
</font>








## 4. Complete the pipeline exercise
If you did not reach the [Uppmax pipeline exercise](uppmax-pipeline) yesterday, please switch to that exercise and complete the first 5 chapters of it. Stop when you have completed the 5th chapter, "5. Scripting a dummy pipeline" and come back here.
