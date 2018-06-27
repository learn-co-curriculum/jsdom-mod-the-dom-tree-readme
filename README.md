# The DOM Is a Tree

## Problem Statement

Recall that DOM programming is using JavaScript to:

1. Ask the DOM to find an HTML element or elements in the rendered page
2. Remove the selected element(s) or add a new element next to the selected element
3. Adjust a property of the selected element(s)

In previous lessons we were given the command to find the HTML element we
wanted to delete. In this lesson, we'll learn the _methods_ on the DOM we need
to use  to find (or "select") the HTML element.

To understand those _methods_, we need to take a quick moment to talk about how
the DOM tree (i.e. what we see in the 'Elements' panel of our DevTools) is used
to help the DOM's _methods_ find the right nodes.

## Objectives

1. Define the computer science version of "tree"
2. Describe how the DOM works as a tree
3. Explain the DOM _methods_ used to find HTML elements

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

## Practice exploring the DOM

Our tree is organized in such a way that a node's metadata doesn't interfere
with finding its children. Not only does providing additional information about
a node make it more _useful_, it also makes it easier to find.

### Finding a Node

JavaScript exposes a few ways of finding DOM nodes more or less directly,
courtesy of the `document` object.

#### `document.getElementById()`

This method provides the quickest access to a node, but it requires that we know
something very specific about it — its `id`. Since IDs must be unique, this
method only returns one element. (If you have two elements with the same ID,
this method returns the first one — keep your IDs unique!) Given the following
DOM tree

``` html
<div>
  <h5 id="greeting">Hello!</h5>
</div>
```

we could find the `h5` element with `document.getElementById('greeting')`.
Notice how the `id` that we pass to `getElementById` is identical to the `id` in
`<h5 id="greeting">`. We can assign properties to HTML nodes (or elements)
simply by including them between the `<>` tags at the start of the element (so
not in the `</h5>` tag, in this case).

**Try it out!**

Open up your web inspector (command+option+j on OS X) and find an element on the
page — make note of its `id`. Then open up your console, type
`document.getElementById('theIdYouTookNoteOf')`, and check out your handy dandy
DOM node.

#### `document.getElementsByClassName()`

This method, as its name implies, finds elements by their `className`. Unlike
`id`, `className` does not need to be unique; as such, this method returns an
HTMLCollection (basically a list of DOM nodes — note that it is _not_ an array,
even though it has a `length` property) of all the elements with the given
class. You can iterate over an HTMLCollection with a simple `for` loop.

Given the following DOM tree

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

we could find all of the elements with the class name "banner" by calling
`document.getElementsByClassName('banner')`.

**Try it out!**

Inspect the web page again, this time making note of a className. Get all
elements with that className and give 'em a look. Remember, you can use a `for`
loop to loop through them. (You can also assign the return value of
`document.getElementsByClassName()` to a variable: `var elements =
document.getElementsByClassName('banner')`.)

#### `document.getElementsByTagName()`

Suppose you don't know an element's ID but you do know its tag name (the tag
name is the main thing between the `<>`, e.g., `'div'`, `'h1'`, `header`, `article` etc.).
Since tag names aren't unique, this method returns an HTMLCollection of 0 to
many nodes with the given tag.

**Try it out!**

Explore the DOM in console by typing `document.getElementsByTagName('div')`.
Remember, you can iterate through these elements using a simple `for` loop.

### Finding a Node Without Knowing Anything About It

What if we know next to nothing about an element? Or what if we're just
interested in finding out more about the child nodes of a given element? This is
where our knowledge of trees and nested data structures comes in handy!

Given the following DOM tree (which you can find in `index.html` — feel free to
open the file up in your browser to play along!)

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

how would we go about changing only the second "Hello!" to "Goodbye!"? We
can't just iterate over `document.getElementsByTagName('div')`, checking for
whether `textContent` is set to `"Hello!"`, because we'd inadvertently change all three
"Hello!"s. More importantly, the DOM might change (more on that later), and we
want to make sure that we're updating the right element.

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
