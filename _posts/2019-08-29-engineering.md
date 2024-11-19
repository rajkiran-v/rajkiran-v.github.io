---
title:  "DS Interview Study Guide Part II: Software Engineering"
category: posts
date: 2019-08-29
excerpt: "Part II of my guide to data science interviews, focusing on algorithms, data structures, and general programming knowledge and best practices."
---

This post continues my series on data science interviews. One of the major difficulty of
doing data science interviews is that you must show expertise in a wide variety of
skills. In particular, I see four key subject areas that you might be asked about during
an interview: 

1. Statistics
2. Software Engineering/Coding
3. Machine Learning
4. "Soft" Questions

This post focuses on software engineering & coding. It will be primarily a resource for
aggregating content that I think you should be familiar with. I will mostly point to
outside sources for technical exposition and practice questions. 

I'll link to these as appropriate throughout the post, but I thought it would be helpful
to put up front a list of the primary resources that I've used when studying for
interviews. Some of my favorites are:

- [Data Structures and Algorithms in Python][dsa_python], for a good introduction to
  data structures such as linked lists, arrays, hashmaps, and so on. It also can give
  you good sense of how to write idiomatic Python code, for building fundamental
  classes.
- [SQLZoo][sqlzoo] for studying SQL and doing practice questions. I particularly like
  the "assessments".
- [Cracking the Coding Interview][CtCI] for lots of practice questions organized by
  subject, and good general advice for the technical interviewing process.

I also use coding websites like LeetCode to practice various problems. I also look on
Glassdoor to see [what kinds of problems][glassdoor] people have been asked.

As always, I'm working to improve this post, so please do leave comments with feedback.

# What Languages Should I Know?

In this section of data science interviews, your are generally asked to implement things
in code. So, which language should you do it in? Generally, the best answer is
(unsurprisingly) that **you should work in Python**. The next most popular choice is R;
I'm not very familiar with R, so I can't really speak to it's capabilities. 

There are a few reasons you should work in Python:

1. It's widely adopted within industry.
2. It has high-quality, popular packages for working with data (see `pandas`, `numpy`,
   `scipy`, `statsmodels`, `scikit-learn`, `matplotlib`, etc).
3. It bridges the gap between academic work (e.g. using NumPy to build a fast solver for
   differential equations) and industrial work (e.g. using Django to build webservices).
   
This is far from an exhaustive list. Anyways, I mostly work in Python. I think it's a
nice language because it is clear and simple to write.

If you want to use another language, you should make sure that you can do everything you
need to - this includes reading & writing data, cleaning/munging data, plotting,
implementing statistical and machine learning models, and leveraging basic data types
like hashmaps and arrays (more on those later).

I think if you wanted to do your interviews in R it would be fine, so long as you can do
the above. I would strongly recommend against languages like MATLAB, which are
proprietary and not open-source. 

Languages like Java can be tricky since they might not have the data-oriented libraries
that Python has. For example, I've worked profesionally in Scala, and am very
comfortable manipulating data via the Spark API within it, but still wouldn't want to
have to use it in an interview; it just isn't as friendly for general-purpose hacking as
Python.

So is Python all you need? Well, not quite. You should also be familiar with SQL for
querying databases; we'll get into that later. I don't think the dialect you use
particularly matters. [SQLZoo][sqlzoo] works with MySQL, which is fine. Familiarity with
bash and shell-scripting is useful for a data scientist in their day-to-day work, but
generally isn't asked about in interviews. For the interviews, I'd say if you know one
general-purpose language (preferably Python, or R if need be) and SQL, then you'll be
fine.


# General Tips for Coding Interviews

Coding interviews are notorious for being high-stress, so it's important that you
practice in a way that will maximize your comfort during the interview itself - you
don't want to add any unnecessary additional stress into an already difficult
situation. There are a wide variety of philosophies and approaches to preparing yourself
for and executing a successful interview. I'm going to talk about some points that
resonate with me, but I'd also recommend reading [Cracking the Coding Interview][CtCI]
for a good discussion. Of course, this isn't the final word on the topic - there are
endless resources available online that address this.

## How to Prepare

When preparing for the interview, make sure to practice in an environment similar to the
interview environment. There are a few aspects of this to keep in mind.

- Make sure that you replicate the **writing environment** of the interview. So, if
  you'll be coding on a whiteboard, try to get access to a whiteboard to practice. At
  least practice on a pad of paper, so that you're comfortable with handwriting code -
  it's really quite different than using a text editor. If you'll be coding in a Google
  Doc, practice doing that (protip: used a monospaced font). Most places I've
  interviewed at don't let you evaluate your code to test it, so you have to be prepared
  for that.
- **Time yourself!** It's important to make sure you can do these things in a reasonable
  amount of time. Generally, these things last 45 minutes per "round" (with multiple
  rounds for on-site interviews). Focus on being efficient at implementing simple ideas,
  so that you don't waste a bunch of time with your syntax and things like that.
- **Practice talking.** If you practice by coding silently by yourself, then it might
  feel strange when you're in the interview and have to talk through your process. The
  best is if you can have a friend who is familiar with interviewing play the
  interviewer, so that you can talk to them, get asked questions, etc. You can also
  record yourself and just talk to the recorder, so that you get practice externalizing
  your thoughts.


There are some services online that will do "practice" interviews for you. When I was
practicing for a software engineer interview with Google, I used [Gainlo][gainlo] for
this - they were kind of expensive, but you interview with real Google software
engineers, which I found helpful. 

However, the interviews for a software engineering position at Google are very
standardized in format. I haven't used any of the services that do this for data
science, and the interviews you'll face are so varied. Therefore, I imagine it is harder
to do helpful "mock interviews". If you've used any of these services, I'd be very
curious to hear about your experience.


## Tips for Interviewing

There are some things it's important to keep in mind as you do the interview itself.

- **Talk about your thought process.** Don't just sit sliently thinking, then go and
  write something on the board. Let the interviewer into your mind so that they can see
  how you are thinking about the problem. This is good advice at any point in a
  technical interview.
- **Start with a simple solution you have confidence in.** If you know that you can
  quickly write up a suboptimal solution (in this case, maybe insertion sort), then do
  that! You can discuss _why_ that solution is sub-optimal, and they will often
  brainstorm with you about how to improve it. That said, if you are just as confident
  in writing up something more optimal (say, quicksort) then feel free to jump right to
  that. 
- **Sketch out your solution before doing real code.** This is not necessary, but
  sometimes for complicated stuff it's nice to write out your approach in pseudocode
  before jumping into real code. This can also help with exposing your thought process
  to the interviewer, and making sure they're on board with how you're thinking about
  it.
- **Think about edge cases.** Suppose they ask you to write a function that sorts a
  list. What if you're given an empty list? What if you're given a list of
  non-comparable things? (In Python, this might be a list of lists.) What does your
  function do in this case?  Is that what you _want_ it to do? There's no right answer
  here, but you should definitely be thinking about this and asking the interview how
  they want the function to behave on these cases.
- **Be sure to do a time complexity analysis on your solution.** They want to know that
  you can think about efficiency, so unless they explicitly ask you not to do this, I'd
  recommend it. We'll discuss more about what this means below.

For a more thorough discussion of preparation and day-of techniques, I'd recommend
[Cracking the Coding Interview][CtCI].

## Tips for Coding

There are few things specifically in how the interviewee writes code that I think are
worth mentioning. This kind of stuff usually isn't a huge deal, but if you write good
code, it can show professionalism and help leave a good impression.

- **Name your variables well.** If the variable is the average number of users per
  region, use `num_users_per_region`, or `users_per_region`, not `avg_usr` or
  `num_usr`. Unlike in mathematics, it's good to have long, descriptive variables.
- **Use built-ins when you can!** Python already _has_ functions for sorting, for
  building cartesian products of lists, for implementing various models (in
  `statsmodels` and `scikit-learn`), and endless other things. It also has some cool
  data structures already implemented, like the [`heap`][pyheap] and
  [`queue`][pyqueue]. Get to know the `itertools` module; it has lots of usefull stuff.
  if you can use these built-ins effectively, it demonstrates skill and knowledge
  without adding much effort on your part.
- **Break things into functions.** If one step of your code is sorting a list, and you
  can't use the built-in `sorted()` function, then write a separate function `def
  sort()` before you write your main function. This increases both readability and
  testability of code, and is essential for real-world software.
- **Write idiomatic Python.** This is a bit less important, but make sure to iterate
  directly over iterables, don't do `for i in range(len(my_iterable))`. Also,
  familiarize yourself with `enumerate` and `zip` and know how to use them. Know how to
  use list compreshensions, and be aware that you can do a similar thing for
  dictionaries, sets, and even arguments of functions - for example, you can do
  `max(item for item in l if item % 2 == 0)` to find the maximum even number in l. Know
  how to do string formatting using either `.format()` for `f`-strings in Python
  3.[^fnote_py3]
  
I'm only scratching the surface of how to write good code. It helps to read code that
others have written to see what you don't know. You can also look at code in large
open-source libraries.
  
With all that said, let's move on to some of the content that might be asked about in
these interviews.

# Working with Data

One of the fundamental tasks of a data scientist is to load, manipulate, clean, and
visualize data in various formats. I'll go through some of the basic tasks that I think
you should be able to do, and either include or link to Python implementations. If you
work in R, or any other language, you should make sure that you can still do these
things in your preferred language. 

In Python, the key technologies are the packages pandas (for loading, cleaning, and
manipulating data), numpy (for efficiently working with unlabeled numeric data), and
matplotlib (for plotting and visualizing data). 

## Loading & Cleaning Data

[This tutorial on DataCamp][datacamp_pandas] nicely deals with the basics of using
`pd.read_csv()` to load data into Pandas. It is also possible to load from other
formats, but in my experience writing to and from comma- or tab-separated plaintext is
by far the most common approach for datasets that fit in memory.[^fnote_parquet]

For example, suppose you had the following data in a csv file:

```
name,age,country,favorite color
steve,7,US,green
jennifer,14,UK,blue
franklin,,UK,black
calvin,22,US,
```

You can copy and paste this, into Notepad or whatever text editor you
like[^fnote_emacs], and save it as `data.csv`.

You should be able to 
- load in data from text, whether it is separated by commas, tabs, or some other
  arbitrary character (sometimes things are separated by the "pipe" character `|`). In
  this case, you can just do `df = pd.read_csv('data.csv')` to load it. 
- Filter for missing data. If you wanted to find the row(s) where the age is missing,
  for example, you could do `df[df['age'].isnull()]`
- Filter for data values. For example, to find people from the US, do `df[df['country'] == 'US']`
- Replace missing data; use `df.fillna(0)` to replace missing data with zeros. Think for
  yourself about how you would want to handle missing data in this case - does it make
  sense to replace everything with zeros? What _would_ make sense?
  
Dealing with missing data is, in particular, an important problem, and not one that has
an easy answer. [Towards Data Science][tds_missing_data] has a decent post on this
subject, but if you're curious, there's a lot to read about and learn here.

More advanced topics in pandas-fu include [using `groupby`][groupby-fu], joining
dataframes (this is called a "merge" in pandas, but works the same as a SQL join), and
[reshaping data][reshaping]. 

As I said before, loading and manipulating data is one of the fundamental tasks of a
data scientist. You should probably be comfortable doing most or all of these tasks if
asked. Pandas can be a bit unintuitive, so I'd recommend practicing if you aren't
already comfortable with it. Doing slicing and reshaping tasks in numpy is also an
important skill, so make sure you are comfortable with that as well.

## Visualization

Another essential aspect of data work is visualization. Of course, this is an entire
field unto itself; here, I'll mostly be focusing on the practical aspects of making
simple plots. If you want to start to learn more about the overarching principles of the
visual representation of data, [Tufte's book][tufte] is the classic in the field.

In Python, the fundamental tool used for data visualization is the library
`matplotlib`. There exist many other libraries for more complicated visualization tasks,
such as `seaborn`, `bokeh`, and `plotly`, but the only one that you really _need_ to be
comfortable with (in my opinion) is `matplotlib`.

You should be comfortable with:
- plotting two lists against one another
- changing the labels on the x- and y-axis of your plot, and adding a title
- changing the x- and y-limits of your plot
- plotting a bar graph
- plotting a histogram
- plotting two curves together, labelling them, and adding a legend

I won't go through the details here - I'm sure you can find many good guides to each of
these online. The [matplotlib pyplot tutorial][pyplot_tutorial] is a good place to
start.[^fnote_pyplot]

It's worth noting that you can plot directly from pandas, by doing `df.plot()`. This
just calls out to matplotlib and plots your dataframe; I will often find myself both
plotting from the pandas `DataFrame.plot()` method as well as directly using
`pyplot.plot()`. They work on the same objects, and so you can use them together to make
more complicated plots with multiple values plotted.


# Data Structures & Algorithms

Designing and building effective software is predicated on a solid understanding of the
basic data structures that are available, and familiarity with the ways that they are
employed in common algorithms. For me, learning this material opened up the world of
software engineering - it illuminated the inner workings of computer languages. It also
helped me understand the pros and cons of various approaches to problems, in ways that I
wouldn't have been able to before.

This subject is fundamental to software engineering interviews, but for data scientists,
its importance can vary drastically from role to role. For engineering-heavy roles, this
material can make up half or more of the interview, while for more statistician-oriented
roles, it might only be very lightly touched upon. You will have to use your judgement
to determine to what extent this material is important to you.

I learned this material when I was interviewing by reading the book [Data Structures and
Algorithms in Python][dsa_python].[^fnote_dsa2] It's really a great book - it has good, clear
explanations of all the important topics, including complexity analysis and some of the
basics of the Python language. I can't recommend it highly enough if you want to get
more familiar with this material.[^fnote_dsa] You can buy it, or look around online for
the PDF - it shouldn't be too hard to find.

## Time and Space Complexity Analysis

Before you begin writing algorithms, you need to know how to analyze their
complexity. The "complexity" of an algorithm tells you how the amount of time (or space)
that the algorithm takes depends on the size of the input data. 

It is formalized using the so-called "big-O" notation. The precise mathematical
definition of $$\mathcal{O}(n)$$ is somewhat confusing, so you can just think of it
roughly as meaning that an algorithm that is $$\mathcal{O}(n)$$ "scales like $$n$$"; so,
if you double the input size, you double the amount of time it takes. If an algorithm is
$$\mathcal{O}(n^3)$$, then, doubling the input size means that you multiply the time it
takes by $$2^3 = 8$$.[^fnote_bigo] You can see how even a $$\mathcal{O}(n^2)$$ algorithm wouldn't
work for large data; even if it runs in a reasonable amount of time (say, 5 seconds)for
10,000 points, it would take about 15,000 years to run on 1 billion data
points. Obviously, this is no good. 

So complexity analysis is critical. You don't want to settle for a $$\mathcal{O}(n^2)$$
solution when a $$\mathcal{O}(n)$$ or $$\mathcal{O}(n \log n)$$ solution is available. I
won't get into how to do the analysis here, besides saying that I often like to annotate
my loops with their complexity when I'm writing things. For example, here's a (slow)
approach to finding the largest k (unique) numbers in a list:

```python
def get_top_k(k, input_list):
    top_k = []
    for _ in range(k):  # happens k times
        remaining = [num for num in input_list if num not in top_k]  # O(n)
        if remaining:
            top_remaining = max(remaining)  # O(n)
            top_k.append(top_remaining)  # O(1)
    return top_k
```

I know that the outer loop happend `k` times, and since finding the maximum of a list is
$$\mathcal{O}(n)$$, the total task is $$\mathcal{O}(nk)$$.[^fnote_asymptotics] To learn
more about how to do complexity analysis, I'd look at [DS&A][dsa_python], [Cracking the
Coding Interview][CtCI], or just look around online - I'm sure there are plenty of good
resources out there.

You can also consider not just the time of computation, but the amount of memory (space)
that your algorithm uses. This is not quite as common as time-complexity analysis, but
is still important to be able to do. 

A very useful resource for anyone studying for a coding interview is the [big-O cheat
sheet][bigO], which shows the complexity of access, search, insertion, and deletion for
various data types, as well as the complexity of searching algorithms, and a lot more. I
often use it as a reference, but of course it's important that you understand _why_ (for
example) an array has $$\mathcal{O}(n)$$ insertion. Just memorizing complexities won't
help you much.

## Arrays & Hashmaps

In my opinion, the two essential data structures for a data scientist to know are
the array and the hashmap. In Python, the `list` type is an array, while the `dict` type
is a hashmap. Since both are used so commonly, you have to know their properties if you
want to be able to design efficient algorithms and do your complexity analysis
correctly.

**Arrays** are a data type where a piece of data (like a string) is linked to an index
(in Python, this is an integer, starting with 0). I won't go too deep into the details
here, but for arrays, the important thing to know is that getting any element of an
array is easy (i.e. doing `mylist[5]` is $$\mathcal{O}(1)$$, so it doesn't depend on the
size of the array) but adding elements (particularly in the beginning or middle of the
array) is difficult; doing `mylist.insert(k, 'foo')` is $$\mathcal{O}(n-k)$$, where
$$k$$ is the position you wish to insert at.[^fnote_linked]

Arrays are what we usually use when we're building unordered, unlabelled collections of
objects in Python. This is fine, since insertion at the end of an array is fast, and
we're often accessing slices of arrays in a complicated fashion (particularly in
numpy). I generally use arrays by default, without thinking too much about it, and it
generally works out alright.

**Hashmaps** also link values to keys, but in this case the key can be anything you
want, rather than having to be an ordered set of integers. In Python, you build them by
specifying the key and the value, like `{'key': 'value'}`. Hashmaps are magical in that
accessing elements _and_ adding elements are both
$$\mathcal{O}(1)$$.[^fnote_array_hashmap] Why is this cool? Well, say you wanted to
store a bunch of people's names and ages. You might think to do a list of tuples:

```python
names_ages = [('Peter', 12), ('Kat', 25), ('Jeff', 41)]
```

Then, if you wanted to find out Jeff's age, you would have to iterate through the list
and find the correct tuple:

```python
for name, age in name_ages:  # happens n times
    if name == 'Jeff':
        print(f"Jeff's age is {age}")
```

This is $$\mathcal{O}(n)$$ - not very efficient. With hashmaps, you can just do 

```python
name_ages = {'Peter': 12, 'Kat': 25, 'Jeff': 41}
print(f"Jeff's age is {name_ages['Jeff']}")  # O(1)! Wow!
```

It might not be obvious how cool this is until you see how to use it in
problems. [Cracking the Coding Interview][CtCI] has lots of good problems on hashmaps,
but I'll just reproduce some of the classics here. I think it's worth knowing these,
because they really can give you an intuitive sense of when and how hashmaps are
valuable.

The first classic hashmap algorithm is **counting frequencies of items in a list.** That
is, given a list, you want to know how many times each item appears. You can do this via
the following:

```python
def get_freqs(l):
    freqs = {}
    for item in l:  # happens O(n) times
        if item not in freqs:  # This check is O(1)! Wow!
            freqs[item] = 1
        else:
            freqs[item] += 1  # Also O(1)! Wow!
    return freqs
```

Try and think of how you'd do this _without_ hashmaps. Probably, you'd sort the list,
and then look at adjacent values. But sorting is, at best $$\mathcal{O}(\log n)$$. This
solution does it in $$\mathcal{O}(n)$$! 

Another classic problem that is solved with hashmaps is to **find all repeated elements
in a list.** This is really just a variant of the last, where you look for elements that
have frequency greater than 1. 

```python
def get_repeated(l):
    f = get_freqs(l)
    return [item for item in f if f[item] > 1]
```

Now, if you only need _one_ repeated element, you can be efficient and just terminate on
the first one you find. For this, we'll use a `set`, which is just a `dict` with values
of `None`. That is to say, **sets are also hashmaps**. The important thing to know is
that adding to them and checking if something is in them are both $$\mathcal{O}(1)$$.

```python
def get_repeated(l):
    items = set()
    for item in l:  # happens O(n) times
        if item not in items:  # This check is O(1)! Wow!
            items.add(item)
        else:
            return(item)
    return None  # if this happens, all elements are unique
```

The last one we'll do is a bit trickier. You're given a list of numbers, and a "target",
and your task is to find a pair of numbers in the list that add up to the target. Try
and think for yourself how you'd do this - the fact you use hashmaps is a big hint. You
should be able to do it in $$\mathcal{O}(n)$$.

Have you thought about it? When I first encountered this one I had to look up the
answer. But here's how you do it in $$\mathcal{O}(n)$$:

```python
def get_sum_pair(l, target):
    nums_set = set()
    for num in l:
        other_num = target-num
        if other_num in nums_set: 
            return (num, other_num)
        nums_set.add(num)   # no-op if num is already there
    return None
```

Note that `other_num = target-num` is the number that you would need to complete the sum
pair; using a hashmap, you can check in $$\mathcal{O}(1)$$ if you've already seen it!
Wow!

Hopefully you get it - hashmaps are cool. Go on LeetCode, or pop open [your favorite
data structures book][dsa_python], or even [Cracking the Coding Interview][CtCI], and
get some practice with them. 

## Sorting & Searching

Sorting and searching are two of the basic tasks you have to be familiar with for any
coding interview. You can go into a lot of depth with these, but I'll stick to the
basics here, because that's what I find most helpful.

### Sorting

**Sorting** is a nice problem in that the statement of the problem is fairly
straightforward; given a list of numbers, reorder the list so that every element is less
than or equal to the next. There are a number of approaches to sorting. The naive
approach is called [**insertion sort**][insertion]; for example, it is what most people
do when sorting a hand of cards. It has some advantages, but is $$\mathcal{O}(n^2)$$ in
time, and so is not the most efficient available.

The two most common fast sorting algorithms are [**quicksort**][quicksort] and
[**mergesort**][mergesort]. They are both $$\mathcal{O}(n \log n)$$ in
time,[^fnote_sort] and so scale close-to-linearly with the size of the list. I won't go
into the implementation details here; there are plenty of good discussions of them
available on the internet.

When thinking about sorting, it's also worth considering space complexity -
can you sort without needing to carry around a second sorted copy of the list? If so,
that's a significant advantage, especially for larger lists. It's also worth thinking
about worst-case vs. average performance - how does the algorithm perform on a randomly
shuffled list, and how does it perform on a list specifically designed to take the
maximum number of steps for that algorithm to sort? Quicksort, for example, is actually
$$\mathcal{O}(n^2)$$ in the worst case, but is $$\mathcal{O}(n \log n)$$ on
average. Again, you can look to the [big-O cheat sheet][bigO] to make sure you're
remembering all your complexities correctly.

### Searching

The problem of **searching** is often stated as **given a sorted list `l` and an object
`x`, find the index at which an element `x` lives.** (You should immediately ask: What
should I return if `x` is not in `l`?)The name of the game here is **binary
search**. You basically split the list, then if the number is greater than the split,
search the top; otherwise, search the bottom. This is an example of a _recursive
algorithm_, so the way it's written can be a bit opaque to those not used to looking at
recursive code. Once I can wrap my head around it, I find it quite elegant. The
important thing to know is that this search is $$\mathcal{O}(\log n)$$, which means that
you don't touch every element in the list - it's very fast, even for a large list. The
key to this is that the list is already sorted - if it's not sorted, then you're out of
luck; you've got to check every element to find `x`.

There are tons of examples of binary search in Python online, so I won't put one
here. That said, I have found it interesting to see how thinking in terms of binary
search can help you in a variety of areas. 

For example, suppose you had some eggs, and worked in a 40-story building, and wanted to
know the highest floor you could drop the egg off of without it breaking (it's kind of a
dumb example cause the egg would probably break even on the first floor, but pretend
it's a super-tough egg.) You could drop it from the first floor, and see what
happens. Say it doesn't break. Then drop it from the 40th, and see what happens. Say it
does break. Then, you bisect and use the midpoint - drop from the 20th floor. If it
breaks here, you next try the 10th - if it doesn't you next try the 30th. This allows
you to find the correct floor much faster than trying each floor in succession.

Sorting and searching are fundamental algorithms, and have been well studied for
decades. Having a basic fluency in them shows a familiarity with the field of computers
science that many employers like to see. In my opinion, **you should be able to quickly
and easily implement the three sorting algorithms above, and binary search,** in Python,
or whatever your language of choice is.


# Working with SQL

Finally, let's talk a bit about SQL. SQL is a tool used to interact with so-called
"relational" databases, which just means that each row in a table has certain values
(columns), and that those values have the same type for each row (that is, the schema is
uniform throughout the table).[^fnote_nosql] It is not exactly a language, it's more
like a family of languages. There are many "dialects" which all have slight differences,
but they behave the same with regards to core functionality; for example, you can do

```sql
SELECT column FROM table WHERE columns = 'value'
```

in any SQL-like language.[^fnote_ansi] Modern data-storage and -access solutions like
Spark and Presto are very different from older databases in their underlying
architecture, but still use a SQL dialect for accessing data.

Solving problems in SQL involves thinking in a quite different way than solving a
similar problem on an array in Python. There is no real notion of iteration, or at least
it's not easily accessible, so most of the complicated action happens via table joins. I
used [SQLZoo][sqlzoo], and particularly the "assessments", to practice my SQL and get it
up to snuff. LeetCode also has a SQL section (I think they call it "database").

It's essential to know SQL as a working data scientist. You'll almost certainly use it
in your day-to-day activities. That said, it's not always asked in the interviews, so
you might clarify with the company whether they will ask you SQL questions.

## A Note on Dialects

There are many dialects of SQL, and changing the dialect changes things like (for
example) how you work with dates. It's worth asking the company you're interviewing with
what dialect they want you to know, if they have one in mind. If you're just writing SQL
on a whiteboard, then I would be surprised if they were picky about this; I would just
say something like "here I'd use `DATE(table.dt_str)` or whatever the string-to-date
conversion function is in your dialect". In this case it's just details that move
around, but the big picture is generally the same for different dialects.


# Conclusion

Coding interviews are stressful. From what I can tell, that's just the way it is. For
me, the best antidote to that is being well-prepared. I think companies are moving more
towards constructive, cooperative interview formats, and away from the classic Google
brain-teaser kind of questions, which helps with this, but you can still expect to be
challenged during these interviews.

Remember to be kind to yourself. You'll probably fail many times before you
succeed. That's fine, and is what happens to almost everyone. Just keep practicing, and
keep learning from your mistakes. Good luck!



<!-------------------------------- FOOTER ----------------------------> 


[dsa_python]: https://www.amazon.com/Structures-Algorithms-Python-Michael-Goodrich/dp/1118290275/

[sqlzoo]: https://sqlzoo.net/

[CtCI]: http://www.crackingthecodinginterview.com/

[datacamp_pandas]: https://www.datacamp.com/community/tutorials/pandas-read-csv

[tds_missing_data]: https://towardsdatascience.com/how-to-handle-missing-data-8646b18db0d4

[groupby-fu]: https://wesmckinney.com/blog/groupby-fu-improvements-in-grouping-and-aggregating-data-in-pandas/

[reshaping]: https://hackernoon.com/reshaping-data-in-python-fa27dda2ff77

[tufte]: https://www.edwardtufte.com/tufte/books_vdqi

[pyplot_tutorial]: https://matplotlib.org/3.1.1/tutorials/introductory/pyplot.html

[insertion]: https://en.wikipedia.org/wiki/Insertion_sort

[quicksort]: https://en.wikipedia.org/wiki/Quicksort

[mergesort]: https://en.wikipedia.org/wiki/Merge_sort

[bigO]: https://www.bigocheatsheet.com/

[gainlo]: http://www.gainlo.co/#!/

[coderpad]: https://coderpad.io/

[glassdoor]: https://www.glassdoor.com/Interview/san-francisco-data-scientist-interview-questions-SRCH_IL.0,13_IM759_KO14,28.htm

[pyqueue]: https://docs.python.org/3/library/queue.html

[pyheap]: https://docs.python.org/3.7/library/heapq.html

[^fnote_py3]: You should be using Python 3 at this point, but also be familiar with the
    differences between 2 and 3, and be able to write code in Python 2 if need be.

[^fnote_parquet]: For "big data" stored in the cloud, an efficient format called Parquet
    is the standard. In my experience, however, it's uncommon to work with parquet files
    directly in Pandas; you often read them into a distributed framework like Spark and work
    with them in that context.

[^fnote_emacs]: The correct answer is, of course, emacs.

[^fnote_pyplot]: `pyplot` is an API within matplotlib that was designed in order to
    mimic the MATLAB plotting API. It is generally what I use; I begin most of my matplotlib
    work with `from matplotlib import pyplot as plt`. I only rarely need to `import
    matplotlib` direct, and that's generally for configuration work.
    
[^fnote_dsa]: It goes well beyond what you'll need for a data science interview,
    however - it gets into tree structures, graphs (and graph traversal algorithms), and
    other more advanced topics. I'd recommend focusing on complexity analysis, arrays,
    and hashmaps as the most important data structures that a data scientist will use
    day-to-day. 
    
[^fnote_dsa2]: I read the book when preparing for a software engineer interview at
    Google, so I picked up a lot more than was necessary for a data science interview. I
    still find the material helpful, however, and it's nice to be able to demonstrate
    that you have gone above and beyond in a realm that data scientists sometimes
    neglect (efficient software design).

[^fnote_asymptotics]: It's a bit weird to use _both_ $$n$$ and $$k$$ in your
    complexity - mathematically, what this means is that we consider them separate
    variables , and we can take the limit of either one independently from the
    other. If, for example, you knew that $$k = n/4$$, so you always wanted the top
    quarter of the list, then this would be $$\mathcal{O}(n^2)$$, since $$n/4 =
    \mathcal{O}(n)$$.

[^fnote_linked]: I'm glossing over some details here - the numbers I quote above are for
    a fixed-size array. So, if you build up an array by adding elements at the end, it
    may seem like you get to just do a bunch of $$\mathcal{O}(1)$$ `.append`s, but in
    reality, you have to occasionally resize the array to make more space, which slows
    things down to an average append time of $$\mathcal{O}(n)$$. If you want a list-like
    type where inserting elements is easy ($$\mathcal{O}(1)$$) but accessing elements is
    difficult ($$\mathcal{O}(n)$$), then you want a _linked list_. Linked lists aren't
    as important for data scientists to use, so I won't get into them much here.
    
[^fnote_array_hashmap]: You might wonder why we would ever use an array over a hashmap
    if hashmaps are strictly superior with respect to their complexity. It's a good
    question. The answer is that arrays take up less space (they don't have to store the
    keys, only the values) and they are much easier to work with in code (they look
    cleaner, and are more intuitive for unordered data). Furthermore, if you had a
    hashmap that linked integers `0` through `10` to strings, and you wanted to change
    the element at key `5`, then you'd have to go through what is currently at keys
    `5` through `10`, and increment their keys by one, so you would end up back at an
    inefficient insertion algorithm like you have with arrays.
    
[^fnote_bigo]: This is only approximately true, or rather it is is _asymptotically_
    true; this scaling law holds in the limit as $$n\rightarrow\infty$$.
    
[^fnote_sort]: This is true _on average_; see the section below for a discussion of
    average vs. worst-case complexity.
    
[^fnote_nosql]: Non-relational database formats, like HBase and NoSQL, basically
    function like giant hashmaps; they have a single "key", and then the "value" can
    contain arbitrary data - you don't have to have certain columns in there. The
    advantage of this is flexibility, but the disadvantage is that sorting and filtering
    are slower because the database doesn't have a pre-defined schema.
    
[^fnote_ansi]: Technically, SQL is an ANSI Standard that many different dialects
    implement - so, to call yourself a SQL dialect, you must have features defined by
    this standard, like the `SELECT`, `FROM`, and `WHERE` clauses shown above.
