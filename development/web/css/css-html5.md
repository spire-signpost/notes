### CSS - CSS and HTML5

A brief introduction to CSS and its usage with HTML5.

#### Contents
  * Intro
  * CSS and HTML5
    * content categories
  * References

#### Intro
A **Cascading Style Sheet**, or CSS, allows us to define stylistic characteristics for our HTML. In effect, it helps us define how our HTML is displayed and rendered. The colours used, font sizes, borders, padding, margins, links, and so on.

#### CSS and HTML5
With CSS and HTML5, we may consider elements relative to defined content categories.

Structures traditionally defined as **block-level** are not now technically applicable for new elements in HTML5. Instead, we now have a slightly more complex model called **content categories**. This includes three primary types of content categories,

  * **main content** categories - describe common content rules shared by many elements
  * **form-related content** categories - describe content rules common to form-related elements
  * **specific content** categories - describe rare categories shared by only a small number of elements, often in a specific context

##### content categories
These content categories include consideration for the following content groupings in HTML,

  * **Metadata content** - Elements belonging to the metadata content category modify the presentation or the behavior of the rest of the document, set up links to other documents, or convey other *out of band* information. These elements currently include,
    * `<base>`, `<command>`, `<link>`, `<meta>`, `<noscript>`, `<script>`, `<style>`, `<title>`
  * **Flow content** - Elements belonging to the flow content category typically contain text or embedded content, for example
    * `<a>`, `<article>`, `<canvas>`, `<figure>`, `<footer>`, `<header>`, `<main>`...
  * **Sectioning content** - Elements belonging to the sectioning content model create a section in the current outline that defines the scope of `<header>` elements, `<footer>` elements, and *heading* content. Elements include,
    * `<article>`, `<aside>`, `<nav>`, `<section>`
  * **Heading content** - defines the title of a section, whether marked by an explicit sectioning content element or implicitly defined by the heading content itself. Elements include,
    * `<h1>`, `<h2>`, `<h3>`, `<h4>`, `<h5>`, `<h6>`, `<hgroup>`

**NB**: out of band means that the specified data etc is not, effectively, sent as part of the plain text...

  * **Phrasing content** - defines the text and the mark-up it contains. For example, such elements can include
    * `<audio>`, `<canvas>`, `<code>`, `<img>`, `<label>`, `<script>`, `<video>`...
    * other elements can belong to this category if certain conditions are met. e.g.
      * `<a>` - if it contains only phrasing content
  * **Embedded content** - imports another resource or inserts content from another mark-up language or namespace into the document. Such elements include the following,
    * `<audio>`, `<canvas>`, `<embed>`, `<iframe>`, `<img>`, `<math>`, `<object>`, `<svg>`, `<video>`
  * **Interactive content** - includes elements that are specifically designed for user interaction such as
    * `<a>`, `<button>`, `<details>`, `<embed>`, `<iframe>`, `<keygen>`, `<label>`, `<select>`, `<textarea>`
    * additional elements, available under specific conditions, include
      * `<audio>`, `<img>`, `<input>`, `<menu>`, `<object>`, `<video>`
  * **Form-associated content** - includes elements contained by a form parent element. This can either be a containing `<form>` element or the id of a form specified as an attribute. Applicable elements include
    * `<button>`, `<input>`, `<label>`, `<select>`, `<textarea>`...
    * there are also several sub-categories, including
      * *listed*, *labelable*, *submittable*, *resettable*

##### Image - Content Categories

![HTML5 Content Categories](../media/images/html5-content-categories.png)

Source - [MDN - Content Categories](https://developer.mozilla.org/en-US/docs/Web/Guide/HTML/Content_categories)

#### References
* [MDN - CSS](https://developer.mozilla.org/en-US/docs/Web/CSS)
* [Perishable Press - Barebones Web Templates](https://perishablepress.com/bare-bones-htmlxhtml-document-templates/)
* [W3 CSS](http://www.w3.org/Style/CSS/)
* [W3 Schools - CSS](http://www.w3schools.com/css/default.asp)
