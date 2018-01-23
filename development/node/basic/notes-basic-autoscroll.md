### Notes - Basic - Autoscroll

A basic example of using autoscroll with JavaScript, and rendering with HTML.

#### Contents
* Intro
*

#### intro
An autoscroll container for vertical content is particularly useful for rendering with HTML. It allows the UI to focus on pertinent content without blocking the view for the user.

#### container
So, a container might be represented as follows,

```
   ----------     ^     ^
  |          |    |     |
  |     1    |    |     |  scrollTop
  |----------|    |     v
  |          |    |     ^
  |          |    |     |
  |          |    |     |
  |     2    |    |     |  clientHeight
  |          |    |     |
  |          |    |     |
  |----------|    |     v
  |          |    |
  |          |    |
  |          |    |
  |     3    |    |
  |          |    |
  |          |    |  scrollHeight
  |          |    |
  |          |    |
   ----------     v
```

where `2` is the viewable part of the container, `1` the top, and `3` the remaining content. As the UI autoscrolls, the content shown in part 2 will change.

For the parts of the container, we may consider their heights as follows

* `scrollHeight` - the full container including visible and non-visible parts
* `clientHeight` - height of the visible container, as shown to the user
* `scrollTop` - number of pixels the UI has scrolled within the overall container (n.b. changes relative to position)

So, as the user scrolls to a given point in the container, the values for `clientHeight` and `scrollTop` may have changed.

#### scroll options
A common option for initiating autoscroll is to perform a calculation between `clientHeight + scrollTop` compared to `scrollHeight`.

If the combined total for `clientHeight + scrollTop` is greater than the value for `scrollHeight`, it's time to autoscroll the content in the UI. This allows the user to automatically view newly added content in the UI container.
