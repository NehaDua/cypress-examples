# Network Requests

Examples of handling AJAX or XHR requests in Cypress, for a full reference of commands, go to [docs.cypress.io](https://on.cypress.io/api)

## [cy.server()](https://on.cypress.io/server)

_Deprecated_ in Cypress v6

To control the behavior of network requests and responses, use the `cy.server()` command.

<!-- fiddle cy.server() - control behavior of network requests and responses -->

```js
// https://on.cypress.io/server

cy.server().should((server) => {
  // the default options on server
  // you can override any of these options
  expect(server).to.be.an('object')
  expect(server.delay).to.eq(0)
  expect(server.method).to.eq('GET')
  expect(server.status).to.eq(200)
  expect(server.headers).to.be.null
  expect(server.response).to.be.null
  expect(server.onRequest).to.be.undefined
  expect(server.onResponse).to.be.undefined
  expect(server.onAbort).to.be.undefined

  // These options control the server behavior
  // affecting all requests

  // pass false to disable existing route stubs
  expect(server.enable).to.be.true
  // forces requests that don't match your routes to 404
  expect(server.force404).to.be.false
  // preserve requests from ever being logged or stubbed
  expect(server.ignore).to.be.a('function')
})

cy.server({
  method: 'POST',
  delay: 1000,
  status: 422,
  response: {},
})

// any route commands will now inherit the above options
// from the server. anything we pass specifically
// to route will override the defaults though.
```

<!-- fiddle-end -->

## [cy.request()](https://on.cypress.io/request)

To make an XHR request, use the `cy.request()` command.

<!-- fiddle cy.request() - make an XHR request -->

```js
// https://on.cypress.io/request
cy.request('https://jsonplaceholder.cypress.io/comments').should(
  (response) => {
    expect(response.status).to.eq(200)
    // the server sometimes gets an extra comment posted from another machine
    // which gets returned as 1 extra object
    expect(response.body)
      .to.have.property('length')
      .and.be.oneOf([500, 501])
    expect(response).to.have.property('headers')
    expect(response).to.have.property('duration')
  },
)
```

<!-- fiddle-end -->

<!-- fiddle cy.request() - with query parameters -->

```js
// will execute request
// https://jsonplaceholder.cypress.io/comments?postId=1&id=3
cy.request({
  url: 'https://jsonplaceholder.cypress.io/comments',
  qs: {
    postId: 1,
    id: 3,
  },
})
  .its('body')
  .should('be.an', 'array')
  .and('have.length', 1)
  .its('0') // yields first element of the array
  .should('contain', {
    postId: 1,
    id: 3,
  })
```

<!-- fiddle-end -->

A request can pass the response data to the next request.

<!-- fiddle cy.request() - pass result to the second request -->

```js
// first, let's find out the userId of the first user we have
cy.request('https://jsonplaceholder.cypress.io/users?_limit=1')
  .its('body') // yields the response object
  .its('0') // yields the first element of the returned list
  // the above two commands its('body').its('0')
  // can be written as its('body.0')
  // if you do not care about TypeScript checks
  .then((user) => {
    expect(user).property('id').to.be.a('number')
    // make a new post on behalf of the user
    cy.request('POST', 'https://jsonplaceholder.cypress.io/posts', {
      userId: user.id,
      title: 'Cypress Test Runner',
      body:
        'Fast, easy and reliable testing for anything that runs in a browser.',
    })
  })
  // note that the value here is the returned value of the 2nd request
  // which is the new post object
  .then((response) => {
    expect(response).property('status').to.equal(201) // new entity created
    expect(response).property('body').to.contain({
      title: 'Cypress Test Runner',
    })
    // we don't know the exact post id - only that it will be > 100
    // since JSONPlaceholder has built-in 100 posts
    expect(response.body)
      .property('id')
      .to.be.a('number')
      .and.to.be.gt(100)
    // we don't know the user id here - since it was in above closure
    // so in this test just confirm that the property is there
    expect(response.body).property('userId').to.be.a('number')
  })
```

<!-- fiddle-end -->

A good idea is to save the response data to be used later in the shared test context.

<!-- fiddle cy.request() - save response in the shared test context -->

```js
// https://on.cypress.io/variables-and-aliases
cy.request('https://jsonplaceholder.cypress.io/users?_limit=1')
  .its('body')
  .its('0') // yields the first element of the returned list
  .as('user') // saves the object in the test context
  .then(function () {
    // NOTE 👀
    //  By the time this callback runs the "as('user')" command
    //  has saved the user object in the test context.
    //  To access the test context we need to use
    //  the "function () { ... }" callback form,
    //  otherwise "this" points at a wrong or undefined object!
    cy.request('POST', 'https://jsonplaceholder.cypress.io/posts', {
      userId: this.user.id,
      title: 'Cypress Test Runner',
      body:
        'Fast, easy and reliable testing for anything that runs in a browser.',
    })
      .its('body')
      .as('post') // save the new post from the response
  })
  .then(function () {
    // When this callback runs, both "cy.request" API commands have finished
    // and the test context has "user" and "post" objects set.
    // Let's verify them.
    expect(this.post, 'post has the right user id')
      .property('userId')
      .to.equal(this.user.id)
  })
```

<!-- fiddle-end -->

## [cy.route()](https://on.cypress.io/route)

_Deprecated_ in Cypress v6

To route responses to matching requests, use the `cy.route()` command.

<!-- fiddle cy.route() - route responses to matching requests -->
<!-- fiddle-markup
<style>
.network-route-btn,
.network-route-post,
.network-route-put {
  margin-bottom: 20px;
}
</style>
-->

```html
<button class="network-route-btn btn btn-primary">Get Comment</button>
<div class="network-route-comment"></div>
<button class="network-route-post btn btn-success">
  Post Comment
</button>
<div class="network-route-post-comment"></div>
<button class="network-route-put btn btn-warning">
  Update Comment
</button>
<div class="network-route-put-comment"></div>
<script>
  // place the example code into a closure to isolate its variables
  ;(function () {
    // we fetch all data from this REST json backend
    const root = 'https://jsonplaceholder.cypress.io'

    function getComment() {
      $.ajax({
        url: `${root}/comments/1`,
        method: 'GET',
      }).then(function (data) {
        $('.network-route-comment').text(data.body)
      })
    }

    function postComment() {
      $.ajax({
        url: `${root}/comments`,
        method: 'POST',
        data: {
          name: 'Using POST in cy.route()',
          email: 'hello@cypress.io',
          body:
            'You can change the method used for cy.route() to be GET, POST, PUT, PATCH, or DELETE',
        },
      }).then(function () {
        $('.network-route-post-comment').text('POST successful!')
      })
    }

    function putComment() {
      $.ajax({
        url: `${root}/comments/1`,
        method: 'PUT',
        data: {
          name: 'Using PUT in cy.route()',
          email: 'hello@cypress.io',
          body:
            'You can change the method used for cy.route() to be GET, POST, PUT, PATCH, or DELETE',
        },
        statusCode: {
          404(data) {
            $('.network-route-put-comment').text(
              data.responseJSON.error,
            )
          },
        },
      })
    }

    $('.network-route-btn').on('click', function (e) {
      e.preventDefault()
      getComment(e)
    })

    $('.network-route-post').on('click', function (e) {
      e.preventDefault()
      postComment(e)
    })

    $('.network-route-put').on('click', function (e) {
      e.preventDefault()
      putComment(e)
    })
  })()
</script>
```

```js
// https://on.cypress.io/route

let message = 'whoa, this comment does not exist'

cy.server()

// Listen to GET to comments/1
cy.route('GET', 'comments/*').as('getComment')

// we have code that gets a comment when
// the button is clicked in scripts.js
cy.get('.network-route-btn').click()

// https://on.cypress.io/wait
cy.wait('@getComment').its('status').should('eq', 200)

// Listen to POST to comments
cy.route('POST', '/comments').as('postComment')

// we have code that posts a comment when
// the button is clicked in scripts.js
cy.get('.network-route-post').click()
cy.wait('@postComment').should((xhr) => {
  expect(xhr.requestBody).to.include('email')
  expect(xhr.requestHeaders).to.have.property('Content-Type')
  expect(xhr.responseBody).to.have.property(
    'name',
    'Using POST in cy.route()',
  )
})

// Stub a response to PUT "comments/*"
cy.route({
  method: 'PUT',
  url: 'comments/*',
  status: 404,
  response: { error: message },
  delay: 500,
}).as('putComment')

// we have code that puts a comment when
// the button is clicked in scripts.js
cy.get('.network-route-put').click()

cy.wait('@putComment')

// our 404 statusCode logic in scripts.js executed
cy.get('.network-route-put-comment').should('contain', message)
```

<!-- fiddle-end -->

## [cy.intercept()](https://on.cypress.io/intercept)

To route responses to matching requests, use the `cy.intercept()` command.

<!-- fiddle cy.intercept() -->
<!-- fiddle-markup
<style>
.network-btn,
.network-post,
.network-put {
  margin-bottom: 20px;
}
</style>
-->

```html
<button class="network-btn btn btn-primary">Get Comment</button>
<div class="network-comment"></div>
<button class="network-post btn btn-success">Post Comment</button>
<div class="network-post-comment"></div>
<button class="network-put btn btn-warning">Update Comment</button>
<div class="network-put-comment"></div>
<script>
  // place the example code into a closure to isolate its variables
  ;(function () {
    // we fetch all data from this REST json backend
    const root = 'https://jsonplaceholder.cypress.io'

    function getComment() {
      $.ajax({
        url: `${root}/comments/1`,
        method: 'GET',
      }).then(function (data) {
        $('.network-comment').text(data.body)
      })
    }

    function postComment() {
      $.ajax({
        url: `${root}/comments`,
        method: 'POST',
        data: {
          name: 'Using POST in cy.intercept()',
          email: 'hello@cypress.io',
          body:
            'You can change the method used for cy.intercept() to be GET, POST, PUT, PATCH, or DELETE',
        },
      }).then(function () {
        $('.network-post-comment').text('POST successful!')
      })
    }

    function putComment() {
      $.ajax({
        url: `${root}/comments/1`,
        method: 'PUT',
        data: {
          name: 'Using PUT in cy.route()',
          email: 'hello@cypress.io',
          body:
            'You can change the method used for cy.route() to be GET, POST, PUT, PATCH, or DELETE',
        },
        statusCode: {
          404(data) {
            $('.network-put-comment').text(data.responseJSON.error)
          },
        },
      })
    }

    $('.network-btn').on('click', function (e) {
      e.preventDefault()
      getComment(e)
    })

    $('.network-post').on('click', function (e) {
      e.preventDefault()
      postComment(e)
    })

    $('.network-put').on('click', function (e) {
      e.preventDefault()
      putComment(e)
    })
  })()
</script>
```

```js
// https://on.cypress.io/intercept

let message = 'whoa, this comment does not exist'

// Listen to GET to comments/1
cy.intercept('GET', '**/comments/*').as('getComment')

// we have code that gets a comment when
// the button is clicked in scripts.js
cy.get('.network-btn').click()

// https://on.cypress.io/wait
cy.wait('@getComment')
  .its('response.statusCode')
  .should('be.oneOf', [200, 304])

// Listen to POST to comments
cy.intercept('POST', '**/comments').as('postComment')

// we have code that posts a comment when
// the button is clicked in scripts.js
cy.get('.network-post').click()
cy.wait('@postComment').should(({ request, response }) => {
  expect(request.body).to.include('email')
  expect(request.headers).to.have.property('content-type')
  expect(response && response.body).to.have.property(
    'name',
    'Using POST in cy.intercept()',
  )
})

// Stub a response to PUT comments/ ****
cy.intercept(
  {
    method: 'PUT',
    url: '**/comments/*',
  },
  {
    statusCode: 404,
    body: { error: message },
    headers: { 'access-control-allow-origin': '*' },
    delayMs: 500,
  },
).as('putComment')

// we have code that puts a comment when
// the button is clicked in scripts.js
cy.get('.network-put').click()

cy.wait('@putComment')

// our 404 statusCode logic in scripts.js executed
cy.get('.network-put-comment').should('contain', message)
```

<!-- fiddle-end -->
