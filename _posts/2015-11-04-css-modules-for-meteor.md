---
title: CSS Modules for Meteor
---

CSS Modules for Meteor
---
tl;dr: CSS modules are awesome - get the package here: [nathantreid:css-modules](https://atmospherejs.com/nathantreid/css-modules).
-
CSS is great. It is a very powerful tool for styling web pages, but it has some big issues, such as global scope.
This wasn't such a big deal when CSS was created and websites were much smaller and less complex, but now that we have experienced the joy of maintaining large, complex web applications, the trend is towards further separation of components and more modular building blocks.

In Meteor, we have React or Blaze Components to handle this for our HTML and JavaScript. We can carefully avoid global scope for most parts of application, but when it comes to our CSS we have no real options.
For example, I recently created a page that made use of a **.hidden** class that applied *display: none* to it's elements.
Unfortunately, it turned out someone else was using a 3rd party stylesheet on the site that also declared a **.hidden** class which applied *display: none **!important***.
This of course caused some easily fixed but annoying display issues, and could easily have been avoided if the only styles being applied to my page were the ones I specified.

[CSS Modules](https://github.com/css-modules/css-modules) solves this and many other issues by creating CSS files that are locally scoped by default.

I'd say more about CSS Modules, but [Glen Maddern already does a terrific job](http://glenmaddern.com/articles/css-modules).

After checking out CSS Modules and getting really excited for it, I looked for a way to use it with Meteor. I couldn't find any existing packages, and while I could probably make use of it via the meteor webpack package, I wanted to stick with straight Meteor, so this past weekend I dove into the Meteor build plugin API.
I took the [basic CSS modules implementation](https://github.com/css-modules/css-modules-loader-core), the [fourseven:scss](https://atmospherejs.com/fourseven/scss) package, and the Build Plugin API docs, and went to work.

I couldn't figure out how to debug Meteor build plugins, so I ended up using a lot of *console.log()*'s to figure things out. Then I ran into an issue where Meteor stopped picking up on changes to my package. I restarted Meteor, removed the package, re-added it, changed the version, changed the name, renamed the folder, and finally rebooted my computer. Thankfully that resolved the issue, and it hasn't come back.

Eventually (this morning) I emerged from my Meteor build plugin pocket universe with my shiny new nathantreid:css-modules plugin.

Meteor doesn't allow us to process .css files, so the CSS modules processor uses the .mss (modular style sheet) extension.
I also includes a JavaScript compiler that enables the use of the CSS Modules **import ... from** syntax. You can only have 1 build plugin per file type and I love ES 6, so my CSS Modules JavaScript compiler also uses Meteor's ecmascript package to transpile the files.

The JavaScript compiler is included in the [nathantreid:css-modules](https://atmospherejs.com/nathantreid/css-modules) package, but if you only want the .mss processing, you can use the
[nathantreid:css-modules-mss](https://atmospherejs.com/nathantreid/css-modules-mss) package instead.

What does it look like? Here is an example:

***client/hello.mss***

``` css
.hello {
    color: red;
    composes: b from "./b.mss";
}
```

***client/b.mss***

``` css
.b {
    font-weight: bold;
}
```

***client/hello.js***

``` javascript
import * as styles from "./hello.mss";

Template.hello.helpers({
    styles: styles
});
```

***client/hello.html***

``` html
<template name="hello">
  <button class="{{styles.hello}}">Click Me</button>
</template>
```

***rendered HTML***

``` html
<button class="_client_hello__hello _client_b__b">Click Me</button>
```

There you go: CSS with unique class names; no more worries about overlapping classes.

The package has been tested on Windows and Mac. It has not yet been tested on Linux, but that should follow shortly.

