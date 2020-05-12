# Traversal

Examples of traversing DOM elements in Cypress, for a full reference of commands, go to [docs.cypress.io](https://on.cypress.io/api)

## [.children()](https://on.cypress.io/children)

To get children of DOM elements, use the `.children()` command.

<!-- fiddle .children() - get child DOM elements -->

```html
<ol class="traversal-breadcrumb breadcrumb">
  <li class="breadcrumb-item"><a href="#">Home</a></li>
  <li class="breadcrumb-item"><a href="#">Library</a></li>
  <li class="breadcrumb-item active">Data</li>
</ol>
```

```js
cy.get('.traversal-breadcrumb')
  .children('.active')
  .should('contain', 'Data')
```

<!-- fiddle-end -->

## [.closest()](https://on.cypress.io/closest)

To get the closest ancestor DOM element, use the `.closest()` command.

<!-- fiddle .closest() - get closest ancestor DOM element -->

```html
<ul class="list-group">
  <li class="list-group-item">
    <span class="badge">14</span>
    Events
  </li>
  <li class="list-group-item">
    <span class="badge traversal-badge">54</span>
    Friends
  </li>
</ul>
```

```js
cy.get('.traversal-badge')
  .closest('ul')
  .should('have.class', 'list-group')
```

<!-- fiddle-end -->

## [.eq()](https://on.cypress.io/eq)

To get a DOM element at a specific index, use the `.eq()` command.

<!-- fiddle .eq() - get a DOM element at a specific index -->

```html
<ul class="traversal-list">
  <li>tabby</li>
  <li>siamese</li>
  <li>persian</li>
  <li>sphynx</li>
  <li>burmese</li>
</ul>
```

```js
cy.get('.traversal-list>li').eq(1).should('contain', 'siamese')
```

<!-- fiddle-end -->

## [.filter()](https://on.cypress.io/filter)

To get DOM elements that match a specific selector, use the `.filter()` command.

<!-- fiddle .filter() - get DOM elements that match the selector -->

```html
<ul class="traversal-nav nav nav-tabs">
  <li class="nav-item">
    <a class="nav-link" href="#">Home</a>
  </li>
  <li class="nav-item">
    <a class="nav-link active" href="#">About</a>
  </li>
  <li class="nav-item">
    <a class="nav-link" href="#">Services</a>
  </li>
</ul>
```

```js
cy.get('.traversal-nav > li a')
  .filter('.active')
  .should('contain', 'About')
```

<!-- fiddle-end -->

## [.find()](https://on.cypress.io/find)

To get descendant DOM elements of the selector, use the `.find()` command.

<!-- fiddle .find() - get descendant DOM elements of the selector -->

```html
<ul class="pagination traversal-pagination">
  <li class="page-item">
    <a class="page-link" href="#">
      <span>&laquo;</span>
    </a>
  </li>
  <li class="page-item">
    <a class="page-link" href="#">1</a>
  </li>
  <li class="page-item">
    <a class="page-link" href="#">2</a>
  </li>
  <li class="page-item">
    <a class="page-link" href="#">3</a>
  </li>
  <li class="page-item">
    <a class="page-link" href="#">4</a>
  </li>
  <li class="page-item">
    <a class="page-link" href="#">5</a>
  </li>
  <li class="page-item">
    <a class="page-link" href="#">
      <span>&raquo;</span>
    </a>
  </li>
</ul>
```

```js
// 5 individual links plus "prev" and "next" links
cy.get('.traversal-pagination')
  .find('li.page-item')
  .find('a')
  .should('have.length', 7)
```

<!-- fiddle-end -->

## [.first()](https://on.cypress.io/first)

To get the first DOM element within elements, use the `.first()` command.

<!-- fiddle .first() - get first DOM element -->

```html
<table class="table traversal-table">
  <thead>
    <tr>
      <th>#</th>
      <th>First Name</th>
      <th>Last Name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td>Jane</td>
      <td>Lane</td>
    </tr>
    <tr>
      <td>2</td>
      <td>John</td>
      <td>Doe</td>
    </tr>
  </tbody>
</table>
```

```js
cy.get('.traversal-table td').first().should('contain', '1')
```

<!-- fiddle-end -->

## [.last()](https://on.cypress.io/last)

To get the last DOM element within elements, use the `.last()` command.

<!-- fiddle .last() - get last DOM element -->

```html
<div class="traversal-buttons">
  <a class="btn btn-default" href="#" role="button">Link</a>
  <button class="btn btn-default" type="submit">
    Button
  </button>
  <input class="btn btn-default" type="button" value="Input" />
  <input class="btn btn-default" type="submit" value="Submit" />
</div>
```

```js
cy.get('.traversal-buttons .btn').last().should('contain', 'Submit')
```

<!-- fiddle-end -->

## [.next()](https://on.cypress.io/next)

To get the next sibling DOM element within elements, use the `.next()` command.

<!-- fiddle .next() - get next sibling DOM element -->

```html
<ul class="traversal-ul">
  <li>apples</li>
  <li class="second">oranges</li>
  <li>bananas</li>
</ul>
```

```js
cy.get('.traversal-ul')
  .contains('apples')
  .next()
  .should('contain', 'oranges')
```

<!-- fiddle-end -->

## [.nextAll()](https://on.cypress.io/nextall)

To get all of the next sibling DOM elements within elements, use the `.nextAll()` command.

<!-- fiddle .nextAll() - get all next sibling DOM elements -->

```html
<ul class="traversal-next-all">
  <li>apples</li>
  <li class="second">oranges</li>
  <li>bananas</li>
  <li>pineapples</li>
  <li>grapes</li>
</ul>
```

```js
cy.get('.traversal-next-all')
  .contains('oranges')
  .nextAll()
  .should('have.length', 3)
```

<!-- fiddle-end -->

## [.nextUntil()](https://on.cypress.io/nextuntil)

To get all of the next sibling DOM elements within elements until another element, use the `.nextUntil()` command.

<!-- fiddle .nextUntil() - get next sibling DOM elements until next el -->

```html
<ul class="healthy-foods">
  <li id="fruits" class="header">Fruits</li>
  <li>apples</li>
  <li>oranges</li>
  <li>bananas</li>
  <li id="veggies" class="header">Vegetables</li>
  <li>cucumbers</li>
  <li>carrots</li>
  <li>corn</li>
  <li id="nuts" class="header">Nuts</li>
  <li>walnuts</li>
  <li>cashews</li>
  <li>almonds</li>
</ul>
```

```js
cy.get('#veggies').nextUntil('#nuts').should('have.length', 3)
```

<!-- fiddle-end -->

## [.not()](https://on.cypress.io/not)

To remove DOM element(s) from the set of elements, use the `.not()` command.

<!-- fiddle .not() - remove DOM elements from set of DOM elements -->

```html
<div class="traversal-disabled">
  <button type="button" class="btn btn-default" disabled="disabled">
    Disabled
  </button>
  <button type="button" class="btn btn-default">Button</button>
</div>
```

```js
cy.get('.traversal-disabled .btn')
  .not('[disabled]')
  .should('not.contain', 'Disabled')
```

<!-- fiddle-end -->

## [.parent()](https://on.cypress.io/parent)

To get parent DOM element of elements, use the `.parent()` command.

<!-- fiddle .parent() - get parent DOM element from DOM elements -->

```html
<p>
  Morbi leo risus, porta ac consectetur ac,
  <mark class="traversal-mark">highlight</mark> vestibulum at eros.
</p>
```

```js
cy.get('.traversal-mark')
  .parent()
  .should('contain', 'Morbi leo risus')
```

<!-- fiddle-end -->

## [.parents()](https://on.cypress.io/parents)

To get parent DOM elements of elements, use the `.parents()` command.

<!-- fiddle .parents() - get parent DOM elements from DOM elements -->

```html
<blockquote>
  <p>Lorem ipsum dolor sit amet, consectetur adipiscing elit.</p>
  <footer>
    Someone famous in
    <cite class="traversal-cite">Source Title</cite>
  </footer>
</blockquote>
```

```js
cy.get('.traversal-cite').parents().should('match', 'blockquote')
```

<!-- fiddle-end -->

## [.parentsUntil()](https://on.cypress.io/parentsuntil)

To get parents DOM element of elements until other element, use the `.parentsUntil()` command.

<!-- fiddle .parentsUntil() - get parent DOM elements from DOM elements until el -->

```html
<ul class="nav clothes-nav">
  <li>
    <a href="#">Clothes</a>
    <ul class="menu">
      <li>
        <a href="/shirts">Shirts</a>
      </li>
      <li class="active">
        <a href="/pants">Pants</a>
      </li>
    </ul>
  </li>
</ul>
```

```js
cy.get('.clothes-nav')
  .find('.active')
  .parentsUntil('.clothes-nav')
  .should('have.length', 2)
```

<!-- fiddle-end -->

## [.prev()](https://on.cypress.io/prev)

To get the previous sibling DOM element within elements, use the `.prev()` command.

<!-- fiddle .prev() - get previous sibling DOM element -->

```html
<ul class="birds list-group">
  <li class="list-group-item">Cockatiels</li>
  <li class="list-group-item">Lorikeets</li>
  <li class="list-group-item active">Cockatoos</li>
  <li class="list-group-item">Conures</li>
  <li class="list-group-item">Eclectus</li>
</ul>
```

```js
cy.get('.birds').find('.active').prev().should('contain', 'Lorikeets')
```

<!-- fiddle-end -->

## [.prevAll()](https://on.cypress.io/prevall)

To get all previous sibling DOM elements within elements, use the `.prevAll()` command.

<!-- fiddle .prevAll() - get all previous sibling DOM elements -->

```html
<ul class="fruits-list">
  <li>apples</li>
  <li>oranges</li>
  <li class="third">bananas</li>
  <li>pineapples</li>
  <li>grapes</li>
</ul>
```

```js
cy.get('.fruits-list')
  .find('.third')
  .prevAll()
  .should('have.length', 2)
```

<!-- fiddle-end -->

## [.prevUntil()](https://on.cypress.io/prevuntil)

To get all previous sibling DOM elements within elements until other element, use the `.prevUntil()` command.

<!-- fiddle .prevUntil() - get all previous sibling DOM elements until el -->

```html
<ul class="foods-list">
  <li id="fruits" class="header">Fruits</li>
  <li>apples</li>
  <li>oranges</li>
  <li>bananas</li>
  <li id="veggies" class="header">Vegetables</li>
  <li>cucumbers</li>
  <li>carrots</li>
  <li>corn</li>
  <li id="nuts" class="header">Nuts</li>
  <li>walnuts</li>
  <li>cashews</li>
  <li>almonds</li>
</ul>
```

```js
cy.get('.foods-list')
  .find('#nuts')
  .prevUntil('#veggies')
  .should('have.length', 3)
```

<!-- fiddle-end -->

## [.siblings()](https://on.cypress.io/siblings)

To get all sibling DOM elements of elements, use the `.siblings()` command.

<!-- fiddle .siblings() - get all sibling DOM elements -->

```html
<ul class="nav nav-pills traversal-pills">
  <li class="active"><a href="#">Home</a></li>
  <li><a href="#">Profile</a></li>
  <li><a href="#">Messages</a></li>
</ul>
```

```js
cy.get('.traversal-pills .active').siblings().should('have.length', 2)
```

<!-- fiddle-end -->
