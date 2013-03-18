# ⚛ Atomic CSS ⚛

Thoughts on a new approach to CSS crafting. In flux. Would love critiques.

## In a nutshell

Atomic.css is a methodology for writing CSS that emphasizes performance. The main goal is shrinking the size of the CSS file that gets sent over the wire, not optimizing CSS selectors (which [Steve Souders suggests isn't worth the time](http://www.stevesouders.com/blog/2009/03/10/performance-impact-of-css-selectors/)). Though I'm still experimenting with different original CSS files, **early tests suggest that we can trim 20% off the filesize of already-compressed CSS.** (I don't mean to suggest we've done lots and lots of testing on this. It's brand new. I'd love your help testing it. I'll revise that number as we get more data in.)

It uses design patterns similar to OOCSS, but pushes 99% of the work to the stylesheet. It leans heavily on preprocessors (namely, Sass).

You should use the Atomic.css methodology if you want fast downloads, coupled with easy-to-write and easy-to-edit CSS.

Atomic CSS flips the duplication found in a normal CSS file, so the duplicated strings are the names of the elements; the DRY'd strings are the longer and more complex CSS properties.

Traditional CSS:

```CSS
input{
  background: #fff;
  font-size: 16px;
  line-height: 1.5;
}

p{
  font-size: 16px;
  line-height: 1.5;
}
```

Traditional CSS, minified and using somewhat arbitrary class names:

```CSS
.ind{background:#fff;font-size:16px;line-height:1.5}.cb{font-size:16px;line-height:1.5}
```    

Atomic CSS:
```CSS
.ind,.cb{font-size:16px}.ind,.cb{line-height:1.5}.ind{background:#fff}
```

As you can see, the duplication involves the class names, not the CSS properties.

Writing this out and maintaining it would be a bear. Thankfully, we have CSS preprocessors to do that for us.

Skip down to "How to write Atomic CSS" for more.


## A quick history lesson (you can skip this)

Classically, CSS files would look something like this:

```CSS
h1 {
  color: #242424;
  font-family: Georgia, serif;
  font-size: 40px;
  line-height: 40px;
}

h1 a {
  color: inherit;
}

input {
  background: #f7f7f7;
  color: #111;
  font-family: "helvetica neue", helvetica, arial, sans-serif;
  font-size: 16px;
  line-height: 22px;
}

p {
  color: #111;
  font-family: "helvetica neue", helvetica, arial, sans-serif;
  font-size: 16px;
  line-height: 22px;
}

.footnote {
  background: #554D54;
  color: #fff;
  font-size: 12px;
  line-height: 1.3;
}

.microcopy {
  font-size: 12px;
  line-height: 1.3;
}
```

You can see, there's a lot of duplication, even in this short section. (Even if *you* would have written the CSS differently / better, recognize that this wouldn't be an uncommon way for someone to write it.) The p and the input are basically the same. And the `.footnote` and `.microcopy` classes are very similar.

Technically, you could have written the `.footnote` and `.microcopy` like this:

```CSS
.footnote, .microcopy {
  font-size: 12px;
  line-height: 1.3;    
}

.footnote {
  background: #554D54;
  color: #fff;
}
```

That would have cut out the duplicate "font-size" and "line-height" lines.

And you could have simplified the `p` and `input` declarations like this:

```CSS
input, p {
  font-family: "helvetica neue", helvetica, arial, sans-serif;
  font-size: 16px;
  line-height: 22px;
}

input {
  background: #f7f7f7;
  color: #111;
}

p {
  color: #111;
}
```

Basically, everything in that shared section is now only written out a single time. The only duplication you're getting is in the names of the elements ("input" and "p"), but those are relatively short.

**But what a hassle.** And, really, can you imagine what it'd take to do that throughout the whole document? That'd mean you'd have to hunt around to find *every* occurance of whatever class you're trying to change if you wanted to make modifications. Blerg.

With CSS preprocessors, though, we have some tools at our disposal that make this easier.

We'll talk about them as we go, but you're probably getting bored at this point. I know I am.


## How to Write Atomic CSS

There are a few steps in writing Atomic CSS.

### 1. Referenced Declarations

The most important concept is that you'll be setting up a number of *referenced declarations*. These are why it's called "atomic" CSS. Think of these as the building blocks we'll be using.

These are just regular CSS classes, with regular CSS properties. You know these. Here are some examples:

```CSS
.fwb{font-weight:bold;}
.ffc{font-family: "helvetica neue", helvetica, arial, sans-serif;}
.ffd{font-family: Georgia, serif;}
```

Note how each one only has one declaration in it, and it has a shortcode (`.fwb`) as its class name. The shortcodes don't *have* to have significance to them, but it probably helps if they do. For example, I used `.ffc` for "font-family, copy"; `.ffd` for "font-family, display".

You can assign these classes names based on a static value:

```CSS
.fs16{font-size:16px;}
.fs24{font-size:24px;}
```

Or a more abstract name (like, "font-size, copy" and "font-size, display"):

```CSS
.fsc{font-size:16px;}
.fsd{font-size:24px;}
```

I haven't yet decided which of these I prefer, but I'm guessing if you're fussy enough to use Atomic CSS, you'd prefer to use the abstract name. So let's roll with that.

We're also going to use the "placeholder" feature of Sass, which just means we'll replace the periods with percent signs. This is just a small optimization, but we'll take it.

So our CSS now looks like this:

```CSS
%cd1{color:#111}
%cd2{color:#343434}
%cl1{color:#f6f6f6}
%cl2{color:#c9c9c9}
%ffc{font-family: "helvetica neue", helvetica, arial, sans-serif}
%ffd{font-family: Georgia, serif}
%fsc{font-size: 16px}
%fsds{font-size: 20px}
%fsd{font-size: 24px}
%fwb{font-weight:bold}
```

Note: We'll eventually be extracting out some of these values to become variables, but I didn't want to throw too much weirdness at you at once. If you're comfortable with variables in Sass, go ahead and set those up up top.

### 2. You got your OOCSS in my Sass. *You got _your_ Sass in my OOCSS!*

For a good explanation of what we're going for here, check out [this piece by Ian Storm Taylor on OOSass](ianstormtaylor.com/oocss-plus-sass-is-the-best-way-to-css/): 

We're going to be giving classes to the various HTML elements in our layout. Nav bars, headlines, callouts, etc. This is just like what you're used to with your regular CSS.

The only think to keep in mind is that, instead of writing out the CSS properties here, you'll be referencing the already-created CSS properties that are up above.

For example, let's say we want to make a headline. It'll be using the "display" font-family we set up (`%ffd`), at the "display" font-size (`%fsd`). And we'll make it bold (`%fwb`). And color-wise, we'll go with our "color, dark, level 1" (`%cd1`)

We'd set that up like this:

```CSS
.h2{@extend %cd1, %ffd, %fsd, %fwb}
```

When Sass processes that, the output will look like this:

```CSS
.h2 {
  color: #111; }

.h2 {
  font-family: Georgia, serif; }

.h2 {
  font-size: 24px; }

.h2{
  font-weight: bold; }
```

And let's say we want to add in a sub-head, which'll be just the same, but with a smaller font-size (`%fsds`). Our .scss file looks like this:

```CSS
.h2{@extend %cd1, %ffd, %fsd, %fwb}
.h3{@extend %cd1, %ffd, %fsds, %fwb}
```

And when we process that, it'll end up looking like this:

```CSS
.h2, .h3 {
  color: #111; }

.h2, .h3 {
  font-family: Georgia, serif; }

.h2 {
  font-size: 24px; }

.h3 {
  font-size: 20px; }

.h2, .h3 {
  font-weight: bold; }
```

Let's look at the filesize savings we get here:

Traditional CSS, compressed
```CSS
.h2{color:#111;font-family:Georgia, serif;font-size:24px;font-weight:bold}.h3{color:#111;font-family:Georgia, serif;font-size:20px;font-weight:bold}
```

Atomic CSS, compressed
```CSS
.h2,.h3{color:#111}.h2,.h3{font-family:Georgia, serif}.h2{font-size:24px}.h3{font-size:20px}.h2,.h3{font-weight:bold}
```


<table>
<tr><td>Traditional CSS, compressed</td><td>148 chars</td><td>0% saved</td></tr>
<tr><td>Atomic CSS, compressed</td><td>117 chars</td><td>20% saved</td></tr>
</table>

I haven't tested this in as wide a variety of situations as I'd like, but it seems like it saves about 20% of the file size pretty consistently.




## Some Background Reading

[http://www.jonathanklein.net/2013/01/how-much-css-should-you-have.html](http://www.jonathanklein.net/2013/01/how-much-css-should-you-have.html)







### Background notes, to be edited out:


## If you aren't me, you can stop reading here. The following is just a braindump with some thoughts on Atomic CSS, some of which directly contradict my latest thinking on it. You've been warned. But if you want to comment on it anyway, feel free!



Variables; Placeholders;


1. A list of the specific colors, type sizes, and box widths used.

/* VARIABLES */

/* setup */
/* values you can change */

$base-type: 16px;
$base-unit: 24px;
$grid-col-width: $base-unit * 8;
$grid-gutter-width: $base-unit * 2;

$color-background: #e6f2bf;
...


/* variables you'll actually use */
/* programattically derived (try not to touch) */

$c0: #000;

$fs1: round($base-type * 4.5);
$fs2: round($base-type * 3.75);
$fs3: round($base-type * 3);
$fs4: round($base-type * 2);
$fs5: round($base-type * 1.5);
$fs6: round($base-type * .75);

$w1: $grid-col-width;
$w2: $grid-col-width * 2 + $grid-gutter-width;
$w3: $grid-col-width * 3 + $grid-gutter-width * 2;
$w4: $grid-col-width * 4 + $grid-gutter-width * 3;
$w5: $grid-col-width * 5 + $grid-gutter-width * 4;
$w6: $grid-col-width * 6 + $grid-gutter-width * 5;

font-weight:bold;line-height:1.2;margin:40px $grid-gutter-width/2 20px}

@mixin gradient($top, $bottom){
  background-image: -moz-linear-gradient(top, #e6f2bf, #99cc00);
  background-image: -webkit-gradient(linear, left top, left bottom, color-stop(0, #e6f2bf), color-stop(1, #99cc00));
  background-image: -webkit-linear-gradient(#e6f2bf, #99cc00);
  background-image: linear-gradient(top, #e6f2bf, #99cc00);
  filter: progid:DXImageTransform.Microsoft.gradient(startColorStr='$top', EndColorStr='$bottom');
}



2. A css stylesheet with LOTS of classes, each defining a speciifc style or c-c-c-combo. These will not be used directly; rather they'll be referenced in the CSS.

Examples:

%br5{border-radius: 5px}
%bgb{background: blue}
%c0{color: $c0}
%fs1{font-size: $fs1}
%fs2{font-size: $fs2}
%fsb{font-size: $base-type}
%fwb{font-weight: bold}
%grd1{@include gradient(gray, black);color:#fff;}


THEN, you have the specific stles, each one calling on each specific class:

3. The actual CSS that controls your layout:

/* headline, site-wide */
.hs{@extend %c0, %fs1, %fwb,  }

/* headline, article */
.ha{@extend %c0, %fs2, %fwb,  }

/* body copy */
.bc{@extend %fsb}




And you run it with:

sass --watch filename.css --style compressed
