# Cookies

Examples of managing cookies in Cypress, for a full reference of commands, go to [docs.cypress.io](https://on.cypress.io/api)

## [cy.getCookie()](https://on.cypress.io/getcookie)

To get a browser cookie, use the `cy.getCookie()` command.

<!-- fiddle cy.getCookie() - get a browser cookie -->

```html
<div id="getCookie">
  <button class="set-a-cookie btn btn-success">Set Cookie</button>
</div>
<script>
  $('.set-a-cookie').on('click', function (e) {
    e.preventDefault()
    document.cookie = 'token=123ABC'
  })
</script>
```

```js
cy.clearCookies()

cy.get('#getCookie .set-a-cookie').click()

// cy.getCookie() yields a cookie object
cy.getCookie('token').should('have.property', 'value', '123ABC')
```

<!-- fiddle-end -->

## [cy.getCookies()](https://on.cypress.io/getcookies)

To get all browser cookies, use the `cy.getCookies()` command.

<!-- fiddle cy.getCookies() - get browser cookies -->

```html
<div id="getCookies">
  <button class="set-a-cookie btn btn-success">Set Cookie</button>
</div>
<script>
  $('.set-a-cookie').on('click', function (e) {
    e.preventDefault()
    document.cookie = 'token=123ABC'
  })
</script>
```

```js
cy.clearCookies()
cy.getCookies().should('be.empty')

cy.get('#getCookies .set-a-cookie').click()

// cy.getCookies() yields an array of cookies
cy.getCookies()
  .should('have.length', 1)
  .its(0)
  .should('include', {
    name: 'token',
    value: '123ABC',
    httpOnly: false,
    secure: false,
  })
  // there are also other properties in the cookie object
  .and('include.keys', ['domain', 'path'])
```

<!-- fiddle-end -->

## [cy.setCookie()](https://on.cypress.io/setcookie)

To set a browser cookie, use the `cy.setCookie()` command.

<!-- fiddle cy.setCookie() - set a browser cookie -->

```js
cy.clearCookies()
cy.getCookies().should('be.empty')

cy.setCookie('foo', 'bar')

// cy.getCookie() yields a cookie object
cy.getCookie('foo').should('have.property', 'value', 'bar')
```

<!-- fiddle-end -->

## [cy.clearCookie()](https://on.cypress.io/clearcookie)

To clear a browser cookie, use the `cy.clearCookie()` command.

<!-- fiddle cy.clearCookie() - clear a browser cookie -->

```html
<div id="clearCookie">
  <button class="set-a-cookie btn btn-success">Set Cookie</button>
</div>
<script>
  $('.set-a-cookie').on('click', function (e) {
    e.preventDefault()
    document.cookie = 'token=123ABC'
  })
</script>
```

```js
cy.clearCookies()
cy.getCookie('token').should('be.null')

cy.get('#clearCookie .set-a-cookie').click()

cy.getCookie('token').should('have.property', 'value', '123ABC')

// cy.clearCookies() yields null
cy.clearCookie('token').should('be.null')

cy.getCookie('token').should('be.null')
```

<!-- fiddle-end -->

## [cy.clearCookies()](https://on.cypress.io/clearcookies)

To clear all browser cookies, use the `cy.clearCookies()` command.

<!-- fiddle cy.clearCookies() - clear browser cookies -->

```html
<div id="clearCookies">
  <button class="set-a-cookie btn btn-success">Set Cookie</button>
</div>
<script>
  $('.set-a-cookie').on('click', function (e) {
    e.preventDefault()
    document.cookie = 'token=123ABC'
  })
</script>
```

```js
cy.clearCookies()
cy.getCookies().should('be.empty')

cy.get('#clearCookies .set-a-cookie').click()

cy.getCookies().should('have.length', 1)

// cy.clearCookies() yields null
cy.clearCookies()

cy.getCookies().should('be.empty')
```

<!-- fiddle-end -->
