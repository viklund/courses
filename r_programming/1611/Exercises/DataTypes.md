---
layout: default
title: 'Data Structures in R'
---

# Introduction<a id="orgheadline1"></a>

There are several different data structured that are commonly used in
R. The different data structures can be seen as different ways to
organise data. In this exercise we learn the basics about the most
commonly used data structure and get on overview or the key data types
(modes) that are found in R. At the end of this exercise you should
know:

-   What are the data types commonly used in R.
-   The basic ideas and differences behind different R data structures.
-   How to create vectors in an interactive R session.
-   Howto create these objects by reading data stored in a file
-   How one can use R functions to determine the structure and mode of an object.
-   How one can subset and work with different data structures in R.

# Data types<a id="orgheadline2"></a>

All entries in the data stuctures found in R will be of a certain type
(or have a certain mode), The four most commonly used data types in R
are: logical, integer, double (often called numeric), and
character. The names hints at what they are.

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

# R data structures<a id="orgheadline6"></a>

Depending on the type of data one needs to store in R different data
structures can be used. The four most commonly used data types in R is
vectors, lists, matrixes and data frames.

## Vectors<a id="orgheadline3"></a>

The most basic data structure in R are vectors. Vectors are
1-dimensional data structures that contain only one type of data
(eg. all entries must have the same mode). To create a vector in R one
can use the function `c()` (concatenate or
combine) as seen below. This example will create a vector named
example.vector with 3 entries in it.

    example.vector <- c(10, 20, 30)

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
double, character). Hence adding a number to a logical vector
will turn the whole vector to a numeric vector.

To check what data type an object is, run the R built-in function
class(), with the object as the only parameter.
TEST

```r
library(limma)
class(example.vector)

[1] "numeric"
```

```ruby
require 'redcarpet'
markdown = Redcarpet.new("Hello World!")
puts markdown.to_html
```

If you for any reason want to have more information about any object
you have stored in your R session the command str() is very helpful. 

    str(example.vector)

    num [1:3] 10 20 30

In the exercise below it is important that you prior to running the
commands in R, try to figure out what you expect the result to be. You
should then verify that this will indeed be the result by running the
command in an R session. In case there is a discrepency between you
expectations and the output make sure you understand why before you
move forward. If you can not figure out how to run the command you can
click the key to reveal code and output that are expected from the
analysis.

## Exercise A1. Create and work with vectors<a id="orgheadline4"></a>

Open R-studio and create two numeric vectors named x and y that are of equal length

<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
x <- c(2, 4 ,7)
y <- c(1, 5, 11)
{% endhighlight %} 
</details>

Answer the following questions

- How many numbers is there in x?
  <details>
  <summary>:key: Click to see an example of how to do this in R</summary>
  {% highlight R %}
  length(x)
  
  [1] 3
  {% endhighlight %} 
  </details>  

- How many numbers in y?
  <details>
  <summary>:key: Click to see an example of how to do this in R</summary>
  {% highlight R %}
  length(y)
  
  [1] 3
  {% endhighlight %} 
  </details>  

3.  What is the sum of all values in x?
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
sum(x)

[1] 13
{% endhighlight %} 
</details>  

4.  What is the sum of y times y?
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
sum(y*y)

[1] 147
{% endhighlight %} 
</details>

5.  What is the result of adding x and y?
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
x + y

[1]  3  9 18
{% endhighlight %} 
</details>

6.  Assign x times y to a new variable named z
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
z <- x * y
{% endhighlight %} 
</details>

7.  How many numbers will this new variable z hold?
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
length(z)

[1] 3
{% endhighlight %} 
</details>

## Exercise B. Modify and subset vectors<a id="orgheadline5"></a>

Create a new character vector that the following words and save it using a suitable name:
apple, banana, orange, kiwi, potato

<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
veggies <- c("apple", "banana", "orange", "kiwi", "potato")
{% endhighlight %} 
</details>

Do the following on your newly created vector.

1.  Select orange
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
veggies[3]
{% endhighlight %} 
</details>

2.  Select all fruits in the vector
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
veggies[-5]
veggies[1:4]
{% endhighlight %} 
</details>

3.  Do the same selection as in question 2 without using index positions
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
veggies[veggies!="potato"]
{% endhighlight %} 
</details>

4.  Convert the character string to a numeric vector
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
as.numeric(veggies)
{% endhighlight %} 
</details>

5.  Create a vector of logic values that can be used to extract every second value from your character vector
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
selection <- c(FALSE, TRUE, FALSE, TRUE, FALSE)
veggies[selection]
{% endhighlight %} 
</details>

Alternative solution
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
selection2 <- c(FALSE, TRUE)
veggies[selection2]
{% endhighlight %} 
</details>

6.  Create a vector containing all the letters in the alphabet (NB! this
    can be done without having to type all letters). Google is your friend
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
letters

[1] "a" "b" "c" "d" "e" "f" "g" "h" "i" "j" "k" "l" "m" "n" "o" "p" "q" "r" "s"
[20] "t" "u" "v" "w" "x" "y" "z"
{% endhighlight %} 
</details>

7.  Extract the letter 14 to 19 from the created vector
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
letters[14:19]

[1] "n" "o" "p" "q" "r" "s"
{% endhighlight %} 
</details>

8.  Extract all but the last letter
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
letters[1:length(letters)-1]
letters[-length(letters)]
[1] "a" "b" "c" "d" "e" "f" "g" "h" "i" "j" "k" "l" "m" "n" "o" "p" "q" "r" "s"
[20] "t" "u" "v" "w" "x" "y"
         
[1] "a" "b" "c" "d" "e" "f" "g" "h" "i" "j" "k" "l" "m" "n" "o" "p" "q" "r" "s"
[20] "t" "u" "v" "w" "x" "y"
{% endhighlight %} 
</details>

9.  Make R print the position of the letter u in the vector
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}
match("u", test)
{% endhighlight %} 
</details>

10. Create a new vector of length one that holds all the alphabet a single entry
<details>
<summary>:key: Click to see an example of how to do this in R</summary>
{% highlight R %}  
paste(letters, sep = "", collapse = "")
{% endhighlight %} 
</details>
