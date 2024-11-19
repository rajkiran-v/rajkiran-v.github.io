---
title: "Blogging in Org Mode"
excerpt: "My workflow for blogging in org mode, with jekyll and org-export."
date: 2019-09-24
categories: 
- post
tags: 
---
I recently transitioned from writing my posts directly in markdown to writing them in
[org mode](https://orgmode.org/), a document authoring system built in GNU Emacs. I learned a lot in the
process, and also built a new org exporter in the process, [`ox-jekyll-lite`.](https://github.com/peterewills/ox-jekyll-lite)[^fn1]


# Org Mode and the Meaning of Life


## What is Org Mode?

Laozi said that the Tao that can be told is not the eternal Tao; I think we can safely
say the same of org mode. Org mode is many things to many people, but at it's core it is
a tool for taking notes and organizing lists. Additional functionality allows for simple
text markup, links, inline images, rendered \\(\LaTeX\\) fragments, and so on. You can embed
and run code blocks within org files, using the powerful [`org-babel`](https://orgmode.org/worg/org-contrib/babel/) package. Some
people have even [written
their Ph.D. thesis in org mode](https://write.as/dani/writing-a-phd-thesis-with-org-mode). It's an amazingly powerful tool, with a passionate
user base that is constantly expanding its capabilities.


## Why Not Markdown?

I like to use org mode for my personal and professional note-taking because it has very
good folding features - you can hide all headings besides the one you're focusing
on. You can even "narrow" your buffer, so that only the heading ("subtree", in org-mode
parlance) that you're working on is present at all.

Org mode also has some nice visual features for writing, such as:

-   rendering \\(\LaTeX\\) fragments inline
-   styling **bold**, <u>underlined</u>, and *italicized* text properly
-   excellent automatic formatting of tables
-   code syntax highlighting in various languages
-   display of images inline

I wrote in markdown (using `markdown-mode` within emacs) for some time, but once I saw
what org mode had to offer, I realized that I needed to transfer my blogging over to
org. In particular, the Emacs mode `markdown-mode` doesn't have a lot of the features that
org mode does, such as inline rendering of math and images or well-built text folding. I
used org for notes, and I realized that it would be much easier to just write in org
instead of trying to get markdown mode to work the way I want it to.

Below is a short clip that shows just some of what org mode has to offer. You'll
want to full-screen it to make the text legible.

<iframe width="560" height="315" src="https://www.youtube.com/embed/MV9LR2LCxAE" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
&nbsp;

Overall, I find the experience of writing in org much more enjoyable than writing in
markdown. Plus, I love hacking on emacs, and moving my blogging workflow over to org
presented me with an opportunity to do just that! So of course, I couldn't resist.


# Org-Export and Jekyll


## Blogging in Jekyll

The primary tool I use to generate my blog is a static-site generator called [Jekyll](https://jekyllrb.com/),
which is written in Ruby. I wrote [a previous post](/_posts/2017-12-29-website.md) describing my process for setting up
my site. [Pelican](https://blog.getpelican.com/) is a similar tool written in Python, and [Hugo](https://gohugo.io/) is a
static-site generator written in Go. We'll talk a bit more about Hugo later.

All of these tools allow the user to write content in simple markdown, with the site
generator doing most of the heavy lifting in generating a full static site behind the
scenes. In Jekyll, the user provided some basic configuration for each post, like a
title, date, and excerpt, and then the them determines the details on how the text is
rendered into fully styled HTML. I use the excellent [minimal mistakes](https://mmistakes.github.io/minimal-mistakes/) theme.

Unfortunately, markdown is not a nicely unified language specification There are many
dialects of markdown, and each has subtle differences, so there is not, in general, one
markdown specification to rule them all. For example, so-called "GitHub-flavored
markdown", which renders markdown from READMEs in GitHub repositories, has certian
quirks that are not shared by the markdown I write for this site. To further complicate
things, the static site generators often have their own quirks - Jekyll requires
particularly-formatted front-matter to specify the configuration for each post, which is
not part of the general markdown specification.

All that is to say, it wasn't a trivial task to find something that converted org to the
exact markdown that I need for my site. But before we jump into the details there, we
should talk a bit about org exporters in general.


## Org-Export

Org mode comes packaged with many built-in "exporters", which convert from the org
format to other text formats, including HTML, \\(\LaTeX\\), iCalendar, and more. It *does*
come with a backend that converts org to markdown, which I hoped would be all that I
need to convert org to markdown.

Unfortunately, the built-in `ox-md` exporter doesn't work very well, for a few reasons. It
falls back on using pure HTML (for example, to generate footnotes) when there are
markdown-native ways of accomplishing the same thing. Also, some things don't work at
all - for example, equation exporting won't work, since markdown requires you to enclose
LaTeX with `\\[` and `\\]`, whereas HTML only requires a single slash.[^fn2]

A quick search will show that there are many tools built to address this problem. Org
exporter backends are designed to be easy to extend, and many users have extended the
markdown backend to work with specific static site generators. The most fully developed
of these is [`ox-hugo`](https://ox-hugo.scripter.co/), which is built to work with the site generator Hugo. This
package in particular would be a big source of the transcoding functions I would use,
but since it is built to be tightly integrated with Hugo, I couldn't just use it out of
the box.

Elsa Gonsiorowski developed a Jekyll-friendly org exporter, called [`ox-jekyll-md`](https://www.gonsie.com/blorg/ox-jekyll.html), which
provided the basis for what I would eventually build. She also wrote [a blog post](https://www.gonsie.com/blorg/ox-jekyll.html) about
it - if you're interested in customizing org exported, I'd recommend giving it a read.


## Building `ox-jekyll-lite`

There are some things that `ox-jekyll-md` does very well, including generating the
Jekyll-specific YAML front matter. However, I found that it lacks a few key features:

-   handling footnotes in a markdown-native way
-   rendering MathJax delimiters with double slashes (to make them markdown-compatable)
-   exporting image links appropriately
-   export link paths relative to the Jekyll root directory

Since these were essential to my blogging workflow, I forked that project and began work
on my org exporter, [`ox-jekyll-lite`](https://github.com/peterewills/ox-jekyll-lite).


### Customizing an Org Export Backend

You can think of an org-export backend as a collection of rules for transforming org
files into other text format. For example, how should underlined text be handled? How
about code blocks? How about \\(\LaTeX\\) snippets? Each of these rules is encapsulated by a
so-called "transcoding function."

Org export backends are built to be highly extensible. If you extend `ox-md`, for example,
then you "inherit" all the transcoding functions that it provides, and you can add or replace
only the functions you want to. For example, part of `ox-jekyll-lite` looks like

```elisp
(org-export-define-derived-backend 'jekyll 'md
  :translate-alist
  '((headline . org-jekyll-lite-headline-offset)
    (inner-template . org-jekyll-lite-inner-template))) 
```

This tells us that we're defining a backend named `jekyll`, which derives from the backend
named `md` (which, if you look, itself derives from the `html` backend). 

In the code above, the `translate-alist` indicates that this backend handles `headline`
objects via the `org-jekyll-lite-headline-offset` method, and handles the `inner-template`
object via `org-jekyll-lite-inner-template`. These functions take in org elements,
returning text that will get dumped into the export buffer.

The transcoding function `org-jekyll-lite-underline` is a particularly simple example:

```elisp
(defun org-jekyll-lite-underline (underline contents info)
  "Transcode UNDERLINE from Org to Markdown.
CONTENTS is the text with underline markup.  INFO is a plist
holding contextual information."
  (format "<u>%s</u>" contents))
```

Extending a backend consists of figuring out which elements you want to handle via
special logic, then writing the appropriate transcoding functions for each.


### Implementation Details for `ox-jekyll-lite`

Most of the more complicated transcoding functions in `ox-jekyll-lite` are not written by
me. They either come from `ox-jekyll-md`, or from `ox-hugo`. For example, I got the
transcoder for footnotes, and for \\(\LaTeX\\) snippets, from `ox-hugo`. 

The most interesting addition that I made was to render file links relative to the root
directory of Jekyll, when possible. For example, if you have an image in your
`assets/images` folder, Jekyll wants you to link to it as `/assets/images/kitties.jpg`, not
with the full path relative to the root directory of your computer's filesystem.

However, when I use `C-c C-l` (along with Helm) to add a link to an org file, it renders
the link with the absolute path.[^fn3] It's important that the link is "correct" for my
machine, so that any images can render inline, and the links are clickable by me when
from my orgfile. But if the links are relative to my filesystem's root in the markdown,
then they won't work within the context of my site. So, we need to "fix" the links as we
export the post to markdown.

I don't get too complicated here - I just have the user specify a custom variable
`org-jekyll-project-root`, which then gets pulled off of the beginning of file paths when
it is present. 

For example, on my machine, this repository is located at
`~/code/jekyll/peterewills.github.io/`, and so if I link to the file
`~/code/jekyll/peterewills.github.io/assets/images/kitties.jpg` in my org file,
`ox-jekyll-lite` will, upon export, transform this to a link to `/assets/images/kitties.jpg`
in the markdown output. This approach is nice and simple, but it doesn't handle relative
links, or the situation where you have multiple Jekyll projects.

Anyways, if you want to give it a try, you can clone it [from GitHub](https://github.com/peterewills/ox-jekyll-lite) and check it
out. You can just load it up and use `C-c C-e j J` to export an org file to a markdown
buffer.

Finally, as a side note, I just have to give a shoutout to the excellent [`s.el`](https://github.com/magnars/s.el) and
[`dash`](https://github.com/magnars/dash.el), which makes working in elisp infinitely more pleasant. Many thanks to Magnar
Sveen for building such nice tools for us all to use.


# My Blogging Workflow

Now, my workflow for writing a post is pretty simple.

1.  Have brilliant idea
2.  Make an org file in the `_posts` directory, named like `YYYY-MM-DD-post-name.org`
3.  Write brilliant words/equations/cat pictures/etc.
4.  Export to markdown via `C-c C-e j j`
5.  Commit & push to GitHub
6.  Profit![^fn4]

The only additional complication, compared to a pure-markdown workflow, is the addition
of the export step; other than that, it's identical. And now I can blog in wonderful,
beautiful org mode instead of clunky markdown.

An important caveat for anyone using org and Jekyll; in order to not have Jekyll stumble
over the org artifacts, you should add `*.org` and `ltximg` to the [list of excluded files](https://github.com/peterewills/peterewills.github.io/blob/master/_config.yml#L13-L17) in
your Jekyll `_config.yml`. You can see mine [on GitHub](https://github.com/peterewills/peterewills.github.io/blob/master/_config.yml).


# Conclusion

If you are just starting to blog, and you love org mode, I'd recommend using Hugo to
build your site, so that you can use the excellent `ox-hugo`. It's a truly org-centric
approach to building a static site, and it's much more fully-featured than any of the
solutions I've found in Jekyll or Pelican.

But, you might want to use Jekyll, because it integrates automagically with GitHub
pages, or perhaps you just like some of the available themes or whatnot. If that's the
case, then I think `org-jekyll-lite` is a reasonable solution for writing your posts in
org. It's lightweight, and you'll probably have to tweak it to fit your particular
needs, but it's small enough that modifying it shouldn't be too hard. Also, you can
always submit an issue on GitHub and I'll see if I can help you out.

I hope this post has inspired you to explore more in org mode! It's a great tool for
organizing notes, tracking agendas/calendars/TODO lists, and for general
writing.[^fn5] Happy blogging, and may the org be with you!


<!----- Footnotes ----->

[^fn1]: As I explain later on, this tool was based on both [`ox-jekyll-md`](https://github.com/gonsie/ox-jekyll-md) and [`ox-hugo`](https://ox-hugo.scripter.co/).
[^fn2]: The double slash is required because markdown interprets the first slash as an escape character.
[^fn3]: You can see an example of adding a link to an image in the org-mode demo video linked above.
[^fn4]: This is actually a lie; I don't make any money from this site.
[^fn5]: There's also the entire subject of [literate programming](http://cachestocaches.com/2018/6/org-literate-programming/), in which code is interwoven with documentation, which I think is a really nice paradigm, and for which org is a natural fit.
