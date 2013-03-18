# ⚛ Atomic.css ⚛

Thoughts on a new approach to CSS crafting. In flux. Would love critiques.

## In a nutshell

Atomic.css is a methodology for writing CSS that emphasizes performance.

It uses design patterns similar to OOCSS, but pushes 99% of the work to the stylesheet. It leans heavily on preprocessors (documentation here uses SASS, but it should work fine with Less).

You should use the Atomic.css methodology if you want fast downloads, coupled with easy-to-write and easy-to-edit CSS.

## A quick history lesson (you can skip this)

Classically, CSS files would look something like this:

    p {
      color: #111;
      font-family: "helvetica neue", helvetica, arial, sans-serif;
      font-size: 16px;
      line-height: 22px;
    }

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

You can see, there's a lot of duplication, even in this short section. (Even if *you* would have written the CSS differently / better, recognize that this wouldn't be an uncommon way for someone to write it.) The p and the input are basically the same. The `.footnote` and `.microcopy` classes are very similar.

Technically, you could have written the `.footnote` and `.microcopy` like this:

    .footnote, .microcopy {
      font-size: 12px;
      line-height: 1.3;    
    }
    
    .footnote {
      background: #554D54;
      color: #fff;
    }

That would have cut out the duplicate "font-size" and "line-height" lines.

And you could have simplified the `p` and `input` declarations like this:

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

Basically, everything in that shared section is now only written out a single time. The only duplication you're getting is in the names of the elements ("input" and "p"), but those are relatively short.

But what a hassle. And, really, can you imagine what it'd take to do that throughout the whole document? That'd mean you'd have to hunt around to find *every* occurance of whatever class you're trying to change if you wanted to make modifications. Blerg.

With CSS preprocessors, though, we have some tools at our disposal that make this easier.

We'll talk about them as we go, but you're probably getting bored at this point. I know I am.


## Writing Atomic CSS

There are a few steps in writing Atomic CSS.




















### Helpful resources

http://ianstormtaylor.com/oocss-plus-sass-is-the-best-way-to-css/




#### Background notes, to be edited out:

This weekend I was reading http://www.jonathanklein.net/2013/01/how-much-css-should-you-have.html, and thought he made some really interesting points, specifically regarding automatically-generated CSS that preprocessors can spit out, and how much bloat that can cause.

I totally agree. But one thing to note is that there's a difference between SASS *mixins* and SASS *extends*. With mixins, the whole block of code gets dropped into your new declaration. With extends, the name of your new class gets added to the earlier declaration, so the only duplicatino is the name of your class. If we give classes short names, we can dramatically reduce our filesizes. (Note: Jonathan Klein is totally correct in his assessment; nothing here contradicts his thoughts. I'm just reframing preprocessors' utility based on a different aspect of the tool that he acknowledges, but didn't go in-depth on.)

Also, let's take a look at the OOCSS pattern. When added directly to the HTML, it creates a pretty bloated file, with lots of presentation names built in to the file and the context of the content. If we can extract that out to the preprocessor, we can make out HTML much more streamlined. If we use the @extend pattern of SASS, we can have that granular control, within our CSS file.

We can run it through a compacting filter with the preprocessor, and create a far smaller file.

So we can create a really compact, concise, and powerful CSS sheet that gives us precise control, that doesn't use a lot of bandwidth.








A few parts.


An emphasis on PERFORMANCE


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









