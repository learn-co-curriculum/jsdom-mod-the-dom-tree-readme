# The DOM Is a Tree

## Problem Statement

Recall that DOM programming is using JavaScript to:

1. Ask the DOM to find an HTML element or elements in the rendered page
2. Remove the selected element(s) or add a new element next to the selected element
3. Adjust a property of the selected element(s)

In previous lessons we were given the command to find the HTML element we
wanted to delete:

```javascript
document.querySelector( selector ).remove()
```

In this lesson, we'll learn the _methods_ on the DOM we need to use to find (or
"select") the HTML element(s) we want to change. In later lessons we'll learn
other methods, like `remove()`, to change the elements.

To understand those "finding" _methods_, we need to take a quick moment to talk
about how the DOM tree (i.e. what we see in the 'Elements' panel of our
DevTools) is used to help the DOM's _methods_ find the right nodes.

## Objectives

1. Describe how the DOM works as a tree
2. Define the computer science version of "Tree"
3. Ask the DOM to find or "select" an HTML element or elements in the rendered page

## Define the Computer Science Version of "Tree"

What do we mean when we say that the DOM is a tree? Trees make a good metaphor
for the DOM because we intuitively understand how we might traverse them.
Starting at the roots, one can climb up the tree and out to the farthest — and
thinnest — branches.

The thicker a branch is, the stronger its connections are: the more it holds
within it. Likewise, the thinner a branch is, the less it holds inside.

The DOM works basically the same way, except we usually talk about the root as
being at the top of the DOM and the leaves being the most deeply nested HTML
elements. So basically, we can imagine a tree turned on its head.

![DOM Tree Graphic](https://curriculum-content.s3.amazonaws.com/skills-front-end-web-development/js-dom-and-events-the-dom-is-a-tree-readme/DOM-model.svg)

## Describe How the DOM Works as a Tree

An interesting property of trees is that every tree can contain _subtrees_,
which we can treat independently of their parent trees.

Practically speaking, the DOM begins at `<html>`, but we should think carefully
about manipulating what's between the `<head></head>` tags. Instead, we can look
at the DOM subtree with its root at `<body>` and only deal with things that will
be visible on the page. Within that tree, we might also deal with subtrees. So,
for example, if we have

``` html
<body>
  <div>
    <p>Hi!</p>
  </div>

  <div>
    <p>Bye!</p>
  </div>
</body>
```

We would have a tree that looks like

``` shell
        body
        /  \
      div   div
      /      \
     p        p
    /          \
 "Hi!"        "Bye!"
```

Pretty simple, right? Similarly, if we had a DOM subtree that looked like

``` html
<div>
  <div>
    <h1>Hello!</h1>
  </div>

  <div>
    <h5>Sup?</h5>
  </div>
</div>
```

We could simply treat it as the tree

``` shell
         div
        /  \
      div   div
      /      \
    h1        h5
    /          \
 "Hello!"     "Sup?"
```

## Ask the DOM To Find Or "select" An HTML Element or Elements in the Rendered Page

Our tree is organized in such a way that a node's metadata doesn't interfere
with finding its children. Not only does providing additional information about
a node via HTML make it more _useful_, it also makes it easier to find.

For the following exercises, you can experiment with _any_ page on the
Internet. But, if you want a familiar page,
[https://learn.co/tracks/javascript-with-style][https://learn.co/tracks/javascript-with-style] is there for you.

### Finding a Node

JavaScript exposes a few ways of finding DOM nodes directly, or via other
neighbors, courtesy of the `document` object.

#### `document.getElementById()`

This one is your laser-beam precision scalpel.

This method provides the quickest access to a node, but it requires that we
know something very specific about it — its `id`. Since IDs must be unique,
this method can only return one element. If you have two elements with the same
ID, this method returns the first one — keep your IDs unique! Also an HTML
validator will detect reuse of an `id` and _will not_ validate your page. So
writing valid HTML is just a good idea.

Given the following DOM tree:

``` html
<div>
  <h5 id="greeting">Hello!</h5>
</div>
```

We could find the `h5` element with `document.getElementById('greeting')`.
Notice how the `id` that we pass to `getElementById` is identical to the `id` in
`<h5 id="greeting">`.

**Try it out!**

Open up your DevTools  (Command + Option + j on OS X) and find an element on
the page — make note of its `id`. Then open up your console, type
`document.getElementById('theIdYouTookNoteOf')`, and check out your handy-dandy
DOM node.

#### `document.getElementsByClassName()`

This one is also very commonly used in DOM programming.

This method, as its name implies, finds elements by their `className`. Unlike
`id`, `className` does not need to be unique; as such, this method returns an
`HTMLCollection` (basically a list of DOM nodes — note that it is _not_ an array,
even though it has a `length` property) of all the elements with the given
class. You can iterate over an `HTMLCollection` with a simple `for` loop.

Given the following DOM tree:

``` html
<!-- the `className` attribute is called `class` in HTML -- it's a bummer -->
<div>
  <div class="banner">
    <h1>Hello!</h1>
  </div>

  <div class="banner">
    <h1>Sup?</h1>
  </div>

  <div class="banner">
    <h5>Tinier heading</h5>
  </div>
</div>
```

We could find all of the elements with the class name "banner" by calling
`document.getElementsByClassName('banner')`.

**Try it out!**

Inspect the web page again, this time making note of a `className`. Get all
elements with that `className` and give 'em a look. On the returned object you
can use the `.length` property to find out how many came back.

If you recall the `for` loop syntax you might try to write a loop which prints
out the `innerHTML` property of every element in the collection. You might find
doing so much easier if you save the results of
`document.getElementsByClassName()` to a variable: `var elements =
document.getElementsByClassName('banner')`.

#### `document.getElementsByTagName()`

Suppose you don't know an element's `id` or `class`, bbut you _do_ know its tag
name (the tag name is the main thing between the `<>`, e.g., `'div'`, `'h1'`,
`header`, `article` etc.).  Since tag names aren't unique, this method returns
an HTMLCollection as well.

**Try it out!**

Explore the DOM in console by typing `document.getElementsByTagName('div')`.
Remember, you can iterate through these elements using a simple `for` loop.

### Finding a Node Without Knowing Anything About It

What if we know next to nothing about an element? Or what if we're just
interested in finding out more about the child nodes of a given element? This
is where our knowledge of trees comes in handy!

Given the following DOM tree:

``` html
<main>
  <div>
    <div>
      <p>Hello!</p>
    </div>
  </div>
  <div>
    <div>
      <p>Hello!</p>
    </div>
  </div>
  <div>
    <div>
      <p>Hello!</p>
    </div>
  </div>
</main>
```

How would we go about changing only the second "Hello!" to "Goodbye!"?

Here we're going to use a hodgepodge of _methods_ to accomplish the goal.

Let's start by getting the `<main>` element

``` javascript
const main = document.getElementsByTagName('main')[0]
```

Then we can get the children of `main` using `main.children`, so we can get the
second child with `main.children[1]`.

``` javascript
const div = main.children[1]
```

Finally, we can get and update our `<p>` element with

``` javascript
// we can call getElementsByTagName() on an _element_
// to constrain the search to its children!
const p = div.getElementsByTagName('p')[0]
```

And lastly we can change an attribute on the node. Elsewhere we've shown how to
remove nodes. In this lesson we both get better at finding them **and** we
change one's attribute!

```javascript
p.textContent = "Goodbye!"
```

Obviously, this way of accessing that text isn't efficient and won't work on all
pages, but it does a good job of demonstrating the basic tools available to us
for finding and manipulating HTML elements.

## Resources

- [MDN - Document Object Model](https://developer.mozilla.org/en-US/docs/Web/API/Document_Object_Model)

## Conclusion

Understanding the tree structure of the DOM helps us navigate its hierarchy. In
subtrees and branches we can find the nodes we need by IDs, class names or tag
names. Once we've selected our elements, we can use JavaScript to manipulate
them.
