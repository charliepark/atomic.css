# atomic.css

Thoughts on a new approach to CSS crafting. In flux. Would love critiques.




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









