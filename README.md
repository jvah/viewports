# viewports.scss

`_viewports.scss` is a SASS microlibrary that makes it easier to manage media queries in responsive layouts.  It does this by allowing you to assign specific styles to named ranges of viewports, instead of repeating `@media` blocks and pixel counts all over.  Works both on the `sass` gem and `libsass`.

## Quick demo

Yet-another github page, and about 10 seconds to pitch this before you close the tab.  So here's the beef:

```scss
// Define what ranges you want and what they're called:
$VIEWPORTS_CONFIG:
    up-to  480px it-is "tiny",
    up-to  768px it-is "small",
    up-to 1024px it-is "medium",
    beyond-that  it-is "large";

// Import the library:
@import "viewports";
```
Then, instead of writing:
```css
@media (min-width: 769px) and (max-width: 1024px) {
    font-size: 150%;
}
```
Or, the more maintainable but even longer SASS variant:
```css
@media (min-width: #{$smallBreakpoint + 1px}) and (max-width: #{$mediumBreakpoint}) {
    font-size: 150%;
}
```
You can write:
```scss
@include viewports(medium) {
    font-size: 150%;
}
```
Which outputs the exact same media query.  You can also name several ranges:
```scss
@include viewports(small medium) { /* ... */ }
```
Or use simple operators to specify the ranges:
```scss
@include viewports(up-to medium) { /* ... */ }
```
That's it!  There's also an [interactive demo](http://jrw.fi/viewports/) of how the library works.  During development, you can set `$VIEWPORTS_DEBUG: true;` to see the currently active range directly in your page (there's an example in the top-left corner of the demo).

## Installation

The library is contained in a single `_viewports.scss` file, which is the only thing you need to include into your project.

 * The cure-for-impatience method is to just download the [main file](https://raw.github.com/jareware/viewports/master/_viewports.scss) and drop it somewhere you can `@import` it from.  That's it, ready to roll.
 * A nicer option is to use [Bower](http://bower.io/) to manage your frontend-related dependencies. `$ bower install viewports` will set the library up at `./bower_components/viewports/`.  To start tracking the dependencies as well (highly recommended), go `$ bower init; bower install viewports --save`.

The only dependency is either of:

 * The `sass` gem (`>= 3.2.5` supported), which you can install with - you guessed it - `$ gem install sass`.
 * `libsass`, for the speed freaks/ruby haters, which is conveniently available through either:
   * [node-sass](https://github.com/andrew/node-sass) (`>= 0.6.5` supported)
   * [grunt-sass](https://github.com/sindresorhus/grunt-sass) (`>= 0.7.0` supported)

## Configuration

To set up your viewports, you must define the `$VIEWPORTS_CONFIG` variable, with a cutesy little english-like syntax, for example:
```scss
$VIEWPORTS_CONFIG:
    up-to  600px it-is "stacked",
    up-to  750px it-is "fluid-1",
    up-to 1000px it-is "fluid-2",
    beyond-that  it-is "static";
```
The pixel amounts (also known as *breakpoints*) and quoted names are yours to choose.  If you want to, you can omit this and just `@import "viewports"`.  That will leave you with sensible default viewport ranges, but you really should pick ones that make sense for your project.  There's a few more bits about that below.

Importing the library depends a bit on your installation method, but it's just the path that varies, for example:

 * `@import "viewports";` if you just dropped the file to the same directory with your main SCSS file(s).
 * `@import "../bower_components/viewports/viewports";` if you installed via [Bower](http://bower.io/) (though the exact number of `..` may vary depending on your project setup; you'll figure it out).

## Picking a range config

One simple option, as demonstrated by the [above example](#configuration), is to name each of your major layout modes.  A common scheme in responsive layouts is to split them into:

  * a **stacked** layout, that caters to small-screen devices that can't afford to display things side-by-side at all
  * a **fluid** layout, that takes up 100% of the available horizontal space on the device
  * a **static** layout, that has a fixed width, so it doesn't look ridiculous on huge desktop displays

You'll note the "fluid" range has been split into two, as it's common to shift things around a bit as your fluid layout gains more space.  You can still target all "fluid" ranges with `@include viewports(fluid-1 fluid-2)`.

Another fine option, as demonstrated by the [first example](#quick-demo), is to look at all the devices you want to target, throw them into a few categories based on their viewport sizes, and give the categories some simple names (from "tiny" to "large", for example).

## Finer details

The library declares a single SASS mixin, which can be invoked in a few ways:

 * `@include viewports(tiny small large)`: Just listing one or more range names gives you a media query that applies when the viewport size falls within any of the listed ranges.
 * `@include viewports(above small)`: You can also name an operator and a *single* range name.  The available operators are:
   * `up-to` for matching all ranges **up to** and **including** the named range
   * `up-from` for matching all ranges **up from** and **including** the named range
   * `below` for matching all ranges **up to** the named range, **excluding** the named range itself
   * `above` for matching all ranges **up from** the named range, **excluding** the named range itself
 * `@include viewports('not' small large)`: Listing one or more range names after the `not` operator produces a media query that applies whenever the viewport size is *not* within any of the listed ranges.  Note that since "not" is a reserved word in SASS, this operator needs to be quoted.

The [interactive demo](http://jrw.fi/viewports/) hopefully makes these quite clear.

## Test suite

To make sure it generates exactly the desired CSS output and nothing else, `_viewports.scss` ships with a good coverage of unit tests, which you can run by going:
```bash
$ git clone git@github.com:jareware/viewports.git
$ npm install
$ npm test
```
Unit testing SASS is surprisingly simple; the test cases are regular SASS files under `test/spec/`, and look something like this:
```scss
@import "../../viewports";

/* it handles a basic "above" query */

@include viewports(above small) {
    p { color: red; }
}

/* should equal */

@media (min-width: 769px) {
    p { color: red; }
}
```
This file can be compiled on its own, and executing `$ sass test/spec/above-1.scss` produces the following CSS output:
```css
/* it handles a basic "above" query */
@media (min-width: 769px) {
  p {
    color: red; } }
/* should equal */
@media (min-width: 769px) {
  p {
    color: red; } }
```
The test runner simply does this for all spec files, makes the part following `/* it ` the name of the test case, and compares the parts separated by `/* should equal */`.  All test cases are executed on both supported compilers: the `sass` gem, and `libsass` (through `node-sass`).

## And finally

![bill](bill.jpg)

*For responsive times... make it __viewports__ time.*

## License

http://opensource.org/licenses/MIT

Hand-crafted with löve by [@jareware](https://twitter.com/jareware) / [jrw.fi](http://jrw.fi/).
