### Notes - Basic - Autoscroll

A basic example of using autoscroll with JavaScript, and rendering with HTML.

#### Contents
* Intro
* Container
* Scroll Options

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

* `scrollHeight` - the full container including visible and non-visible parts, i.e. all rendered content in window regardless of what is visible to the user
* `clientHeight` - height of the visible container, as shown to the user
* `scrollTop` - number of pixels the UI has scrolled within the overall container (n.b. changes relative to position)

So, as the user scrolls to a given point in the container, the values for `clientHeight` and `scrollTop` may have changed.

#### scroll options
A common option for initiating autoscroll is to perform a calculation between `clientHeight + scrollTop` compared to `scrollHeight`.

If the combined total for `clientHeight + scrollTop` equals the value for `scrollHeight`, it's time to autoscroll the content in the UI. This allows the user to automatically view newly added content in the UI container. So, we can now autoscroll the client to the bottom of the scrollHeight container.

We'll also need to consider the height of the new content in our calculation, e.g.

```
              scrollHeight
   ----------     ^     ^
  |          |    |     |
  |          |    |     |  
  |          |    |     |
  |          |    |     |
  |          |    |     |
  |          |    |     |
  |          |    |     |  
  |     1    |    |     |  scrollTop
  |          |    |     |
  |          |    |     |
  |          |    |     |
  |          |    |     v
  |----------|    |     ^
  |          |    |     |
  |          |    |     |
  |     2    |    |     | clientHeight
  |          |    |     |
  |          |    |     v
  |  ------  |    |     ^
  |          |    |     |
  |     3    |    |     |  contentHeight
  |          |    |     |
  -----------     v     v
              scrollHeight
```

If the `scrollTop + clientHeight + contentHeight` equals `scrollHeight`, we can initiate autoscroll.

However, if the user has scrolled back to view previous, older content, it is not good practice to then initiate autoscroll for each new item. We should calculate a threshold where the autoscroll is initiated.
