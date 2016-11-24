---
layout: default
title:  'Schedule'
---

<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#orgheadline4">1. Introduction</a>
<ul>
<li><a href="#orgheadline1">1.1. Data types</a></li>
<li><a href="#orgheadline2">1.2. Vectors in R</a></li>
<li><a href="#orgheadline3">1.3. Basic R operators</a></li>
</ul>
</li>
<li><a href="#orgheadline7">2. Exercise: Creating and working with vectors</a>
<ul>
<li><a href="#orgheadline5">2.1. Create and modify vectors</a></li>
<li><a href="#orgheadline6">2.2. Exercise: Modify and subset vectors</a></li>
</ul>
</li>
</ul>
</div>
</div>


# Introduction<a id="orgheadline4"></a>

There are several different data structured that are commonly used in
R. The different data structures can be seen as different ways to
organise data. In this exercise we will focus on vectors that are the
base data structure in R and will also get on overview of the key data types
(modes) that are found in R. At the end of this exercise you should
know:

-   What are the data types commonly used in R.
-   What is a vector.
-   How to create vectors in an interactive R session.
-   How one can use R functions to determine the structure and mode of an vector.
-   What basic operators you can find in R
-   Howto subset vector using both indexes and operators.
-   Try some of the built-in functions in R.

## Data types<a id="orgheadline1"></a>

From the lecture you might remember that all elements in any data
stuctures found in R will be of a certain type (or have a certain
mode). The four most commonly used data types in R are: logical,
integer, double (often called numeric), and character. The names hints
at what they are.

-   Logical = TRUE or FALSE (or NA)
-   Integer = Numbers that can be represented without fractional component
-   Numeric = Any number that is not a complex number.
-   Character = Text

In many cases the mode of on entry is determined by the content so if
you save the value 5.1 as a variable in R, the variable will by R
automatically be recognised as numeric. If you instead have a text
string like "hello world" it will have the mode character. Below you
will also see examples of how you can specify the mode and not rely on
R inferring the right mode based on content.

## Vectors in R<a id="orgheadline2"></a>

Depending on the type of data one needs to store in R different data
structures can be used. The four most commonly used data types in R is
vectors, lists, matrixes and data frames. We will in this exercise
work only with vectors.

The most basic data structure in R are vectors. Vectors are
1-dimensional data structures that contain only one type of data
(eg. all entries must have the same mode). To create a vector in R one
can use the function `c()` (concatenate or
combine) as seen below. This example will create a vector named
example.vector with 3 entries in it.

    example.vector <- c(10, 20, 30)

NB! If you need more information about the function c() you can always use
the built-in manual in R. Typing `c()` will bring up the
documentation for the function `?c`.

Once you have created this vector in R, you can access it by simply
typing its name in an interactive session.

    example.vector

    [1] 10 20 30

The output generate on screen shows the entries in your vector and the
1 in squared brackets indicates what position in the vector the entry
to the right of it have. In this case 10 is the first entry of the vector.

If we for some reason only wanted to extract the value 10 from this
vector we can use the fact that we know it is the first position to do
so. 

    example.vector[1]

    [1] 10

Since a vector can only contain one data type, all members need to be
of the same type. If you try to combine data of different types into
the same vector, R will not warn you, but instead coerce it to the
most flexible type (From least to most flexible: Logical, integer,
double, character). Hence, adding a number to a logical vector
will turn the whole vector to a numeric vector.

To check what data type an object is, run the R built-in function
class(), with the object as the only parameter.

    class(example.vector)

    [1] "numeric"

If you for any reason want to have more information about any object
you have stored in your R session the command `str()` is very helpful.

    str(example.vector)

    num [1:3] 10 20 30

## Basic R operators<a id="orgheadline3"></a>

As in other programming languages there are a set of basic operators in R. 

<table border="2" cellspacing="0" cellpadding="6" rules="groups" frame="hsides">


<colgroup>
<col  class="org-left" />

<col  class="org-left" />

<col  class="org-left" />

<col  class="org-left" />
</colgroup>
<thead>
<tr>
<th scope="col" class="org-left">Operation</th>
<th scope="col" class="org-left">Description</th>
<th scope="col" class="org-left">Example</th>
<th scope="col" class="org-left">Example Result</th>
</tr>
</thead>

<tbody>
<tr>
<td class="org-left">x + y</td>
<td class="org-left">Addition</td>
<td class="org-left">1 + 3</td>
<td class="org-left">4</td>
</tr>


<tr>
<td class="org-left">x - y</td>
<td class="org-left">Subtraction</td>
<td class="org-left">1 - 3</td>
<td class="org-left">-2</td>
</tr>


<tr>
<td class="org-left">x \* y</td>
<td class="org-left">Multiplication</td>
<td class="org-left">2 \* 3</td>
<td class="org-left">6</td>
</tr>


<tr>
<td class="org-left">x / y</td>
<td class="org-left">Division</td>
<td class="org-left">1 / 2</td>
<td class="org-left">0.5</td>
</tr>


<tr>
<td class="org-left">x ^ y</td>
<td class="org-left">Exponent</td>
<td class="org-left">2 ^ 2</td>
<td class="org-left">4</td>
</tr>


<tr>
<td class="org-left">x %% y</td>
<td class="org-left">Modular arethmetic</td>
<td class="org-left">1 %% 2</td>
<td class="org-left">1</td>
</tr>


<tr>
<td class="org-left">x %/% y</td>
<td class="org-left">Integer division</td>
<td class="org-left">1 %/% 2</td>
<td class="org-left">0</td>
</tr>


<tr>
<td class="org-left">x == y</td>
<td class="org-left">Test for equality</td>
<td class="org-left">1 == 1</td>
<td class="org-left">TRUE</td>
</tr>


<tr>
<td class="org-left">x <= y</td>
<td class="org-left">Test less or equal</td>
<td class="org-left">1 <= 1</td>
<td class="org-left">TRUE</td>
</tr>


<tr>
<td class="org-left">x >= y</td>
<td class="org-left">Test for greater or equal</td>
<td class="org-left">1 >= 2</td>
<td class="org-left">FALSE</td>
</tr>


<tr>
<td class="org-left">x && y</td>
<td class="org-left">Boolean AND for scalar</td>
<td class="org-left">3 >= 2 &&  3 < 10</td>
<td class="org-left">TRUE</td>
</tr>


<tr>
<td class="org-left">x & y</td>
<td class="org-left">The same for vectors</td>
<td class="org-left">&#xa0;</td>
<td class="org-left">&#xa0;</td>
</tr>


<tr>
<td class="org-left">x &vert; &vert; y</td>
<td class="org-left">Boolean OR for scalar</td>
<td class="org-left">1 >= 2  &vert; &vert; 3 < 10</td>
<td class="org-left">TRUE</td>
</tr>


<tr>
<td class="org-left">x &vert;  y</td>
<td class="org-left">The same for vectors</td>
<td class="org-left">&#xa0;</td>
<td class="org-left">&#xa0;</td>
</tr>


<tr>
<td class="org-left">!x</td>
<td class="org-left">Boolean not</td>
<td class="org-left">1 != 2</td>
<td class="org-left">TRUE</td>
</tr>
</tbody>
</table>

Besides these there of course numerous more or less simple functions
that is based on these basic operators available in all R
sessions. For example if we want to add all values in our
example.vector that we discussed earlier, we can do that using
addition:

    example.vector[1] + example.vector[2] + example.vector[3]

    [1] 60

But as we often want to know the sum of a vector we can use the
built-in function sum that adds all values found in a vector.

    sum(example.vector)

    [1] 60

To learn more about a function use the built in R manual as described
earlier. If you do not know the name of a function that you believe
should be found in R, use the function src\_R[:exports
code]{help.search()} or use google to try and identify the name of the
command.

# Exercise: Creating and working with vectors<a id="orgheadline7"></a>

In all exercises on this course it is important that you prior to
running the commands in R, try to figure out what you expect the
result to be. You should then verify that this will indeed be the
result by running the command in an R session. In case there is a
discrepency between your expectations and the actual output make sure
you understand why before you move forward. If you can not figure out
how to, or which command to run you can click the key to reveal example code
including expected output. Also note that in many cases there multiple
solutions that solve the problem equally well.

## Create and modify vectors<a id="orgheadline5"></a>

Open R-studio and create two numeric vectors named x and y that are of
equal length. Use the vectors to answer the questions below. 

    x <- c(2, 4 ,7)
    y <- c(1, 5, 11)

1.  How many numbers are there in the vector x?
    
        length(x)
    
        [1] 3

2.  How many numbers will x + y generate?
    
        length(x + y)
    
        [1] 3

3.  What is the sum of all values in x?
    
        sum(x)
    
        [1] 13

4.  What is the sum of y times y?
    
        sum(y*y)
    
        [1] 147

5.  What do you get if you add x and y?
    
        x + y
    
        [1]  3  9 18

6.  Assign x times 2 to a new vector named z
    
        z <- x * 2

7.  How many numbers will z have, why?
    
        length(z)
    
        [1] 3

8.  Assign the mean of z to a new vector named z.mean and determine the length of z.mean
    
        z.mean <- mean(z)
        length(z.mean)

9.  Create a numeric vector with all integers from 5 to 107
    
        vec.tmp <- 5:107
        vec.tmp
    
        [1]   5   6   7   8   9  10  11  12  13  14  15  16  17  18  19  20  21  22
        [19]  23  24  25  26  27  28  29  30  31  32  33  34  35  36  37  38  39  40
        [37]  41  42  43  44  45  46  47  48  49  50  51  52  53  54  55  56  57  58
        [55]  59  60  61  62  63  64  65  66  67  68  69  70  71  72  73  74  75  76
        [73]  77  78  79  80  81  82  83  84  85  86  87  88  89  90  91  92  93  94
        [91]  95  96  97  98  99 100 101 102 103 104 105 106 107

10. Create a numeric vector with the same length as the previos one, but only containg the number 3
    
        vec.tmp2 <- rep(3, length(vec.tmp))
        vec.tmp2
    
        [1] 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3
        [38] 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3
        [75] 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3 3

## Exercise: Modify and subset vectors<a id="orgheadline6"></a>

Create a new character vector that the following words and save it using a suitable name:
apple, banana, orange, kiwi, potato

    veggies <- c("apple", "banana", "orange", "kiwi", "potato")

Do the following on your newly created vector.

1.  Select orange from the vector
    
        veggies[3]
2.  Select all fruits from the vector
    
        veggies[-5]
        veggies[1:4]
3.  Do the same selection as in question 2 without using index positions
    
        veggies[veggies=="apple" | veggies == "banana" | veggies == "orange" | veggies == "kiwi"]
        veggies[veggies!="potato"]
4.  Convert the character string to a numeric vector
    
        as.numeric(veggies)

5.  Create a vector of logic values that can be used to extract every second value from your character vector
    
        selection <- c(FALSE, TRUE, FALSE, TRUE, FALSE)
        veggies[selection]
    
    Alternative solution, why do this work?
    
        selection2 <- c(FALSE, TRUE)
        veggies[selection2]
6.  Create a vector containing all the letters in the alphabet (NB! this
    can be done without having to type all letters). Google is your friend
    
        letters
    
         [1] "a" "b" "c" "d" "e" "f" "g" "h" "i" "j" "k" "l" "m" "n" "o" "p" "q" "r" "s"
        [20] "t" "u" "v" "w" "x" "y" "z"

7.  Extract the letter 14 to 19 from the created vector
    
        letters[14:19]
    
        [1] "n" "o" "p" "q" "r" "s"

8.  Extract all but the last letter
    
        letters[1:length(letters)-1]
        letters[-length(letters)]
    
         [1] "a" "b" "c" "d" "e" "f" "g" "h" "i" "j" "k" "l" "m" "n" "o" "p" "q" "r" "s"
        [20] "t" "u" "v" "w" "x" "y"
         
        [1] "a" "b" "c" "d" "e" "f" "g" "h" "i" "j" "k" "l" "m" "n" "o" "p" "q" "r" "s"
        [20] "t" "u" "v" "w" "x" "y"

9.  Make R print the position of the letter u in the vector.
    
        which(letters=="u")
    
        [1] 21

10. Create a new vector of length one that holds all the alphabet a single entry
    
        paste(letters, sep = "", collapse = "")

11. Create a numeric vector by sampling 100 numbers from a
    normal distribution with mean 2 and standard deviation 4. 
    
        norm.rand <- rnorm(100, mean = 2, sd = 4)

12. How many of the generated values are negative? 
    
        length(norm.rand[norm.rand<0])
    
        [1] 30

13. In many cases one has data from multiple replicates and different
    treatments in such cases it can be useful to have names of the type:
    Geno\_a\_1, Geno\_a\_2, Geno\_a\_3, Geno\_b\_1, Geno\_b\_2&#x2026;, Geno\_s\_3
    Try to create this such a vector without manually typing it all in.
    
        geno <- rep("Geno", 57)
        needed.letters <- rep(letters[1:19], 3)
        needed.numbers <- rep(1:3, 19)
        temp <- paste(geno, needed.letters, needed.numbers, sep = "_")
        sort(temp)
    
        [1] "Geno_a_1" "Geno_a_2" "Geno_a_3" "Geno_b_1" "Geno_b_2" "Geno_b_3"
         [7] "Geno_c_1" "Geno_c_2" "Geno_c_3" "Geno_d_1" "Geno_d_2" "Geno_d_3"
        [13] "Geno_e_1" "Geno_e_2" "Geno_e_3" "Geno_f_1" "Geno_f_2" "Geno_f_3"
        [19] "Geno_g_1" "Geno_g_2" "Geno_g_3" "Geno_h_1" "Geno_h_2" "Geno_h_3"
        [25] "Geno_i_1" "Geno_i_2" "Geno_i_3" "Geno_j_1" "Geno_j_2" "Geno_j_3"
        [31] "Geno_k_1" "Geno_k_2" "Geno_k_3" "Geno_l_1" "Geno_l_2" "Geno_l_3"
        [37] "Geno_m_1" "Geno_m_2" "Geno_m_3" "Geno_n_1" "Geno_n_2" "Geno_n_3"
        [43] "Geno_o_1" "Geno_o_2" "Geno_o_3" "Geno_p_1" "Geno_p_2" "Geno_p_3"
        [49] "Geno_q_1" "Geno_q_2" "Geno_q_3" "Geno_r_1" "Geno_r_2" "Geno_r_3"
        [55] "Geno_s_1" "Geno_s_2" "Geno_s_3"
