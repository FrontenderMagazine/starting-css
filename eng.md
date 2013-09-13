# Starting to Write CSS

Don't you feel that CSS is not the same anymore? Last few years became a hot
topic and many smart people talked about it. CSS is not that little thing which
front-end developer should do to make the page looks pretty. It's far more then
that. We care about performance and we want to produce better web sites. In this
article I want to share what I learned last few months and what exactly is my
vision about CSS coding. As a programmer I'm really interested in the
architectural part of the things. I feel that the writing of CSS should be
changed and I dig a lot. I search for the good processes, the best principles
and new workflows. This post is like conclusion of journey in the world of CSS.
Many people say that writing CSS is not exactly programming. I'll disagree and
will say that it's equally interesting and challenging.

## CSS Preprocessors

![Preprocessors][What happens when a programmer starts writing CSS - 1]

Ok, let's face it. Writing pure CSS is not the funnies thing in the world. The
preprocessors take something which looks like CSS, make some magic and produce
valid CSS code. This adds one more layer between you and the final styles which
are send to the browser. That's not so bad as it sounds like, because the
preprocessors provide some really helpful features.

### Concatenation

I think that one of the most valuable thing which came is the concatenation of
your files. I'm sure, you know that when you use `@import` in your `.css` file
you actually tell to the browser *please, get that file too*. And it does. It
makes a new request which is kinda bad, because you may have a lot of files.
Making additional requests decrease the performance of you application. If you
use CSS preprocessors this problem is gone. They simply compile all your styles
to one single `.css` file.

### Extending

There are two main CSS preprocessors - [LESS][1] and [SASS][2]. They both
support extending. Yes, it works a little bit different, but the idea is the
same. You make a basic class (usually called mixin) with bunch of properties and
later import these properties inside another selector. For example

    // less
    .bordered(@color: #000) {
        border: dotted 2px @color;
    }
    .header { .bordered; }
    .footer { .bordered(#BADA55); }
    
    // compiles to
    .header {
        border: dotted 2px #000000;
    }
    .footer {
        border: dotted 2px #bada55;
    }

The problem here is that if you define a mixin without parameters i.e. if you
have just

    .bordered {
        border: dotted 2px #000;
    }

this goes to the final compiled CSS file, no matter if it is used or not. It's
like that, because it is a valid selector. In SASS we have a little bit more
flexibility. There are mixins, extends and placeholders (if you want to see the
exact difference between them I strongly recommend checking [this article][3]).
Let's get the following SASS and its compilation:

    // sass
    @mixin bordered($color: #000) {
        border: dotted 2px $color;
    }
    .header { @include bordered; }
    .footer { @include bordered(#BADA55); }

    // compiles to
    .header {
        border: dotted 2px black; 
    }
    .footer {
        border: dotted 2px #bada55; 
    }

It looks almost the same. But if we go to the second use case and define a place
holder:

    // sass
    %bordered {
        border: dotted 2px #000;
    }
    .header { 
        @extend %bordered; 
    }
    .footer { 
        @extend %bordered; 
    }

    // compiles to
    .header, .footer {
        border: dotted 2px #000; 
    }

There are two good things happening. First, there is no `.bordered` class
compiled. Second, SASS combines the selectors and it makes our CSS a little bit
shorter.

### Configuration

Both LESS and SASS support definition of variables. You can later access those
variables and use them as values for your properties.

    // sass
    $brand-color: #009f0A;
    ...
    h1 {
        color: $brand-color;
    }

That's a good feature, because you may store some important things like company
colors or grid widths in one place. If you need to change something you don't
need to go through all your code.

Another handy usage of the variables is interpolation. The following example
demonstrates the idea:

    // sass
    @mixin border($side) {
        border-#{$side}: solid 1px #000;
    }
    .header {
        @include border("left");
    }
    
    // compiles to
    .header {
        border-left: solid 1px #000; 
    }

### Against the preprocessors

* The preprocessor is a tool, i.e. it's one more thing which you have to add to 
your environment. You may want to integrate it into your application. This of 
course requires additional coding.
* If you don't want to mess your code with such a thing, then you will probably 
need a watcher. Another instrument which monitors your files and once something 
is updated fire compilation. If that's the case then you have to run the watcher 
every time when you start working on the project. Maybe you will optimize the 
process over the time, but it's still something that you should care about.
* Very often the developers look only their .less or .sass files. However the 
output is what it matters. You may have elegant and optimized SASS code, but 
this doesn't mean that you will end up with the same beautiful CSS code. You may 
have some really *interesting* specificity problems. So, check the compiled 
version regularly.

## BEM

![BEM][What happens when a programmer starts writing CSS - 2]

Ok, I found a new tool to play with. The preprocessors probably save a lot of
time, but they can't write a good architecture alone. The first thing which I
started thinking of is a naming convention. Let's get the following HTML markup:

    <header class="site-header">
        <div class="logo"></div>
        <div class="navigation"></div>
    </header>

A possible styling may look similar to

    .site-header { ... }
    .logo { ... }
    .navigation { ... }

This will work of course, but it has a problem - reading the CSS you can't
understand that, for example, the logo belongs to the header. You may have
another little logo image used in the footer. The next logical step is to write
a descendant selector.

    .site-header .logo { ... }

However using such selectors is not very good idea, because it tights styles to
specific tags hierarchy. What if I want to move the logo outside the `header`
tag? I'll lose the styling. The other thing which you could do is to add `site-
header` in the name of the logo's class:

.site-header-logo { ... }

That's good, self explanatory, but it doesn't work in all the cases. Later,
during December I may want to use a Christmas version of the logo. So, I can't
write

    .site-header-logo-xmas { ... }

because my logic is to write the selector like that so it matches the nesting of
the tags in the html.

[BEM][4] could be the answer of the situation. It means Block, Element, Modifier 
and creates some rules, which you could follow. Using BEM, our little example 
will be transformed to:

    .site-header { ... } /* block */
    .site-header__logo { ... } /* element */
    .site-header__logo--xmas { ... } /* modifier */
    .site-header__navigation { ... } /* element */

I.e. `site-header` is our block. The logo and the navigation are elements of
that block and the `xmas` version of the logo is modifier. Maybe it looks
simple, but it's really powerful. Once you start using it will find that it
makes your architecture better. The arguments against BEM are mainly because of
the syntax. Yes, maybe it looks a little bit ugly, but I'm ready to make a
sacrifice in the name of the good system.

(good reading: [here][5] and [here][6])

## OOCSS

![OOCSS][What happens when a programmer starts writing CSS - 3]

Once I found BEM I was able to name my classes correctly and I started thinking
about composition. Maybe the first thing which I read was an article about
[Object oriented CSS][7]. Object oriented programming sometimes is about adding
abstractions and CSS language supports that. Using preprocessors or not, OOCSS
is something that you should know about. As a coder I found this concept really
close to my usual programming, in JavaScript for example. There are two main
principles:

### Separate structure and skin

Let's use the following example:

    .header {
        background: #BADA55;
        color: #000;
        width: 960px;
        margin: 0 auto;
    }
    .footer {
        background: #BADA55;
        text-align: center;
        color: #000;
        padding-top: 20px;
    }

There are few styles which are duplicated. We may extract them in another class 
like that:

    .colors-skin {
        background: #BADA55;
        color: #000;
    }
    .header {
        width: 960px;
        margin: 0 auto;
    }
    .footer {
        text-align: center;
        padding-top: 20px;
    }

So, we have an object `colors-skin` which we could extend. The html markup may 
look like that:

    <div class="header colors-skin"> ... </div>
    <div class="colors-skin"> ... </div>
    <div class="footer colors-skin"> ... </div>

There are several benefits of that change:

* We have a class, which may be used several times.
* If we need a change we need to make it in only one place
* We removed duplication in our CSS file, which makes its file size lower

### Separate container and content

The idea here is that every element should have the same styles applied no
matter where it is put in. So, you should avoid the usage of selectors similar
to the following

    .header .social-widget {
        width: 250px;
    }

It's because if you move `.social-widget` outside the `.header` container the
width will be different. In general, that's not a good practice. Especially for
components which are used all over the page. This principle encourage modular
CSS and I strongly recommend to take enough time trying it. Personally, for me,
following the principle means producing better CSS.

### The framework

If you open the [OOCSS repository][8] on GitHub you will see a framework. Yes,
the framework uses object oriented CSS concept and yes, it has a bunch of cool
ready-to-use components. From some time I don't like frameworks. If you think a
bit you will see that the word framework has two parts frame and work. And
indeed you work in a frame. You actually make a deal with that thing and you
have to play by its rule. I prefer to use micro-frameworks or something which
gives me only the base. Of course I don't mean to reinvent the wheel, but I'm
always trying to find the balance. Very often, the ready-to-use solutions lead
to messy and complex systems. My advice is to build things with only one an
specific purpose. If you try to cover as many cases as possible you will end up
with ... you know - a framework.

However, I strongly recommend checking the OOCSS framework. It's an unique piece
of knowledge and maybe it will fit in your needs. The repository is hosted by
[Nicole Sullivan][9]. She is a pioneer in OOCSS and if you have some free time
I'll suggest to check here [presentations/talks][10].

## SMACSS

![SMACSS][What happens when a programmer starts writing CSS - 4]

Another popular concept is [SMACSS][11]. SMACSS stands for scalable and modular
architecture for CSS. [Jonathan Snook][12] introduces something like style guide
for the CSS developers. The idea is to separate your application into the
following categories:

* Base - basic default styles usually used for a simple selectors. Like clearfix 
for example.
* Layout - grids definition
* Module - a group of elements which combined form a module. Like for example 
header or sidebar.
* State - contains different states of elements. Definition of rules if 
particular object is hidden, pressed, expanded etc ...
* Theme - oriented more to the visual parts of the things. Similar like the 
state category.

I don't have experience using SMACSS, but it is quite popular and indeed
promotes good ideas. The very good thing is that it is more like a concept then
a framework. So, you are not tight to any strict rules, classes or components.

## Atomic design

![Atomic][What happens when a programmer starts writing CSS - 5]

Knowing about OOCSS and SMACSS I searched for an appropriate metaphor and very
soon I landed on this [page][13]. It's a presentation of the great concept
*Atomic Design*. Its author is [Brad Frost][14], which is a well known web
developer working mainly in the [responsive][15] and mobile world.

The idea is really interesting. Following some chemistry terminology, we could
say that the basic unit of the matter is the atom. Brad moves this to CSS saying
that our pages are build by atoms. An atom could be

    <label>Search the site</label>

or

    <input type="text" placeholder="enter keyword" />

I.e. atoms contain some basic styling of DOM elements. Like color palette, font
sizes or transitions. Later those parts could be combined into molecules. For
example:

    <form>
        <label>Search the site</label>
        <input type="text" placeholder="enter keyword" />
        <input type="submit" value="search" />
    </form>

So the `form` element contains several atoms. Abstracting the things like that
brings flexibility, because we may use the same atoms to build another molecule.
Together with that, we could reuse the same form in different contexts.

Brad didn't stop there. The organisms are something which is build of molecules.
Following the same approach we may write the following and call it an organism:

    <header>
        <div class="logo">
        <nav>
            <ul>
                <li><a href="#">Home</a></li>
                <li><a href="#">About</a></li>
                <li><a href="#">Contacts</a></li>
            </ul>
        </nav>
        <form>
            <label>Search the site</label>
            <input type="text" placeholder="enter keyword" />
            <input type="submit" value="search" />
        </form>
    </header>

The next thing in the concept are the templates. They are not related to the
chemistry directly, but are put into web context. Once we start combining
different organisms we are building template. Later those templates form our
final pages.

You are probably already using similar approach to build your applications.
However, naming the things in a reasonable manner brings good architecture. You
and all your team mates will have something to catch on, during the development.
Separation of the things to atoms and molecules is kinda important part, because
it improves both, the working process and the maintenance of your web
application.

## OrganicCSS

![OrganicCSS][What happens when a programmer starts writing CSS - 6]

Before a couple of months I wrote an [article][16] about [Organic][17]. It's a
great small framework for JavaScript applications. It's more like a design
pattern, and I personally like it a lot. I even used Organic in several projects
and everything works pretty well. If you are interested in it, I strongly
recommend reading the blog post [here][18].

When I read the Brad Frost's article I was already familiar with similar
concept, because I knew about Organic. Brad's work is amazing, but I decided to
go deeper and try to write my own micro framework based on atomic design
concept. I chose SASS as a preprocessor and create a repository in Github -
[https://github.com/krasimir/organic-css][19].

### Atoms

Let's start with the smallest part of the framework - the atom. Its definition
in [wikipedia][20] is *The atom is a basic unit of matter*. In the context of
CSS, I think that it is a property and its value. For example:

    margin-top: 24px;

Adding atoms just by writing styles directly inside the classes is not what I
wanted. Because if I type something like that

    body {
        margin-top: 24px;
    }
    header {
        margin-top: 24px;   
    }

the preprocessor will leave that as it is. The result which I want to get at the 
end is:

    body, header {
        margin-top: 24px;
    }

In SASS this effect is achievable by using place holders. I.e.

    %margin-top-24 {
        margin-top: 24px;
    }
    body {
        @extend %margin-top-24; 
    }
    header {
        @extend %margin-top-24; 
    }

So, I had to use placeholders. This also means that I had to have a lot of
predefined placeholders, which I can use. At that moment, I decided that the
framework will contain only atoms. And maybe few molecules with generic
functions like the usual `reset.css`, grid definition and so on. I wanted to
write something which acts as a base for the CSS development. Maybe project
after project I'll see some patterns, which could be put in the core, but as a
start I wanted to keep the repo clean and simple.

To make the things a little bit more consistently, I created a mixin for atom
definition. So, here is an example:

    @include define-atom("block") {
        display: block;
    }
    @include define-atom("font-family") {
        font-family: Georgia;
    }

Using this approach I created a bunch of atoms, which I could easily apply in
every project. You can check them [here][21]. I used some good practices from
other frameworks so, not all the credits go to me. There is also a mixin for
combining atoms in a molecule:

    @mixin header { // <- molecule called 'header'
        @include atoms((
            block,
            clearfix,
            font-family
        ));
    }

### Molecules

Molecule is a DOM element which needs styling, but doesn't have children. Or if
it has children they are not directly connected to it. For example `<img
src="logo.jpg" />` could be a molecule. If you find difficult to recognize the
molecules in your page, just think of what is build by atoms. If some element is
build by other molecules it is probably an organel. Just a few lines above I
showed how to define a molecule:

    @mixin login-box { 
        @include atoms((
            block,
            font-size-20,
            margin-top-23,
            bold
        ));
    }

There is something interesting, which I faced with. Let's get the `body` tag.
What it is? Is it a molecule or something else? Sure, it needs some styling via
atoms, but in general contains other molecules. It should be something else. I
made the conclusion that the CSS should be the leading part. I.e. if the `body`
needs few atoms for its styling then it is a molecule, which means that, in
theory, I should not attach any other molecules to it. This may seem a little
bit impractical, but in most of the cases will keep you from using descent
selectors, which is a good sign.

### Organelles

Once you recognize which DOM elements are molecules you will see what organelles
are. For example, the typical `form` element is a great example of organelle. It
contains molecules like label, input or textarea.

    .login-form {
        @include label;
        @include input;
        @include textarea;
    }

The organelles are maybe the first part of the framework, which is tightly
connected to the current application. The atoms and molecules could be
transferred between the different projects while the organelles may not.

### More abstractions

Very often you may want to combine several organelles in something else. If
that's the case then add another abstractions.

    Atom → Molecule → Organelle → Cell → Tissue → Organ → Sys → Organism

It's a matter of choice how you will continue constructing your CSS. I used
OrganicCSS only in one project so far, but I could say that it brings clarity. I
put the different elements in their own directories and named the classes like
that so I can easily find out what exactly I'm working with. For example if
there is an organelle called `header` I simply change it to `o-header`. Later,
when I read the HTML markup I could see that the CSS styles for this element are
in `organelles` folder.

## Conclusion

It was an interesting journey. I don't know if I'm going to use OrganicCSS in
the future but that's not the most important part. The things which I've learned
are what it matters. I knew that I had to change my CSS development process and
I did it. I believe that we should talk more about architecture in CSS. As you
can see we have a lot of good resources out there. We just have to find them,
learn what they do and how they work. Only then we could decide what to use or
not. Even better, when you see the whole picture you are able to create
something which fits better in your needs.

[1]: http://lesscss.org/
[2]: http://sass-lang.com/
[3]: http://krasimirtsonev.com/blog/article/SASS-mixins-extends-and-placeholders-differences-use-cases
[4]: http://bem.info/method/definitions/
[5]: http://bem.info/method/definitions/
[6]: http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/
[7]: https://github.com/stubbornella/oocss/wiki
[8]: https://github.com/stubbornella/oocss
[9]: https://twitter.com/stubbornella
[10]: http://www.youtube.com/watch?v=GhX8iPcDSsI
[11]: http://smacss.com/
[12]: https://twitter.com/snookca
[13]: http://bradfrostweb.com/blog/post/atomic-web-design/
[14]: http://bradfrostweb.com/
[15]: http://bradfrost.github.io/this-is-responsive/index.html
[16]: http://net.tutsplus.com/tutorials/javascript-ajax/organic-development/
[17]: https://github.com/VarnaLab/node-organic
[18]: http://net.tutsplus.com/tutorials/javascript-ajax/organic-development/
[19]: https://github.com/krasimir/organic-css
[20]: http://en.wikipedia.org/wiki/Atom
[21]: https://github.com/krasimir/organic-css/tree/master/src/atoms

[What happens when a programmer starts writing CSS - 1]: img/preprocessors.jpg
[What happens when a programmer starts writing CSS - 2]: img/bem.jpg
[What happens when a programmer starts writing CSS - 3]: img/oocss.jpg
[What happens when a programmer starts writing CSS - 4]: img/smacss.jpg
[What happens when a programmer starts writing CSS - 5]: img/atomic.jpg
[What happens when a programmer starts writing CSS - 6]: img/organic.jpg