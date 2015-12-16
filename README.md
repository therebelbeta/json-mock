# JSON Mock

[![](https://badge.fury.io/js/json-mock.svg)](http://badge.fury.io/js/json-mock) 
[![Join the chat at https://gitter.im/therebelrobot/json-mock](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/therebelrobot/json-mock?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[![Build Status](https://travis-ci.org/therebelrobot/json-mock.svg)](https://travis-ci.org/therebelrobot/json-mock) 
[![Dependency Status](https://david-dm.org/therebelrobot/json-mock.svg)](https://david-dm.org/therebelrobot/json-mock)
[![Code Climate](https://codeclimate.com/github/therebelrobot/json-mock/badges/gpa.svg)](https://codeclimate.com/github/therebelrobot/json-mock)
[![Test Coverage](https://codeclimate.com/github/therebelrobot/json-mock/badges/coverage.svg)](https://codeclimate.com/github/therebelrobot/json-mock)

> Get a full mock REST API with __zero coding__ in __less than 30 seconds__ (seriously)

Created with <3 for front-end developers who need a quick back-end for prototyping and mocking. 

*This is a fork of [typicode/json-server](http://github.com/typicode/json-server). Typicode expressed 
[a desire to keep the json-server API simple](https://github.com/typicode/json-server/pull/82#issuecomment-101393288), 
hence this fork. Refer to Purpose section below.*

The API is ever expanding, to allow for more advanced features. If you have a need that isn't being met, 
please open an issue and I'll take a look. Refer below to the Roadmap to see what's coming.

## Table of Contents

> * [Install](#install)
> * [Rest Routes](#rest-routes)
>   * [Normal Slash Routing (e.g. `/user/12/posts`)](#normal-slash-routing)
>   * [Query String Routing](#query-string-routing)
>   * [Pagination](#pagination)
>   * [Sorting](#sorting)
>   * [Full Text Searching](#full-text-searching)
>   * [Reserved Routes](#reserved-routes)
> * [Additional Features](#additional-features)
>   * [Static File Server](#static-file-server)
>   * [CORS / JSONP](#cors--jsonp)
>   * [Remote Schemas](#remote-schemas)
>   * [Dynamic Data](#dynamic-data)
>   * [Programmatic Use](#programmatic-use)
>   * [Deployment](#deployment)
> * [Purpose](#purpose)
> * [Roadmap](#roadmap)
> * [Contributing](#contributing)
>   * [Contributors](#contributors)
> * [Changelog](#changelog)
>   * [Unreleased](#unreleased)
>   * [v0.1.0 Initial Release 2015-05-13](#v010---initial-release---2015-05-13)
>     * [Added](#added)
> * [License](#license)

## Install

```bash
$ npm install -g json-mock
```

## Usage

Create a `db.json` file

```javascript
{
  "users": [
    { "id": 1, "name": "therebelrobot", "location": "USA"},
    { "id": 2, "name": "visiting-user", "location": "UK"}
  ],
  "posts": [
    { "id": 1, "title": "json-mock", "body":"The internet is cool!", "author": "therebelrobot", "userId": 1 }
  ],
  "comments": [
    { "id": 1, "body": "some comment from author", "votes": 20, "postId": 1, "userId": 1 },
    { "id": 2, "body": "some comment from visitor", "votes": 15, "postId": 1, "userId": 2 }
  ]
}
```

Start JSON Server

```bash
$ json-server db.json
```

Now if you go to [http://localhost:3000/posts/1](http://localhost:3000/posts/1), you'll get

```javascript
{ "id": 1, "title": "json-mock", "author": "therebelrobot", "userId": 1 }
```

Also, if you make POST, PUT, PATCH or DELETE requests, changes will be automatically saved to `db.json`

## REST Routes

Here are all the available routes based on the above json schema. Note the implementation of infinitely nested calls.

### Normal Slash Routing

| Method  | Path | Description |
| ------------- | ------------- | ------------- |
| `GET` | `/users` | Return list of users |
| `GET` | `/users/1` | Return user with id === 1 |
| `GET` | `/users/2` | Return user with id === 2 |
| `GET` | `/users/1/posts` | Return list of any posts with userId === 1 |
| `GET` | `/users/1/posts/1` | Return post with userId === 1 && id === 1 |
| `GET` | `/users/1/posts/1/comments` | Return list of any comments with postId === 1 |
| `GET` | `/users/1/posts/1/comments/1` | Return comment with postId === 1 && id === 1 |
| `GET` | `/users/1/posts/1/comments/2` | Return comment with postId === 1 && id === 2 |
| `GET` | `/users/1/comments` | Return list of any comments with userId === 1 |
| `GET` | `/users/2/comments` | Return list of any comments with userId === 2 |
| `GET` | `/users/1/comments/1` | Return comment with userId === 1 && id === 1 |
| `GET` | `/users/2/comments/2` | Return comment with userId === 2 && id === 2 |
| `GET` | `/posts` | Return list of all posts |
| `GET` | `/posts/1` | Return post with id === 1 |
| `GET` | `/posts/1/comments` | Return list of all comments with postId === 1 |
| `GET` | `/posts/1/comments/1` | Return comment with postId === 1 && id === 1 |
| `GET` | `/posts/1/comments/2` | Return comment with postId === 1 && id === 2 |
| `GET` | `/comments` | Return list of all comments |
| `GET` | `/comments/1` | Return comment with id === 1 |
| `GET` | `/comments/2` | Return comment with id === 2 |
| `POST` | `/users` | Create new user |
| `POST` | `/posts` | Create new post |
| `POST` | `/comments` | Create new comment |
| `PUT` | `/users/1` | Replace user by id |
| `PUT` | `/posts/1` | Replace post by id |
| `PUT` | `/comments/1` | Replace comment by id |
| `PATCH` | `/users/1` | Update user by id |
| `PATCH` | `/posts/1` | Update post by id |
| `PATCH` | `/comments/1` | Update comment by id |
| `DELETE` | `/users/1` | Remove user by id (could cause orphaned posts/comments. Refer to Roadmap below.) |
| `DELETE` | `/posts/1` | Remove post by id (could cause orphaned comments. Refer to Roadmap below.) |
| `DELETE` | `/comments/1` | Remove comment by id |


### Query String Routing

You can use query strings to any normal route to further drill down resources:

| Method  | Path | Description |
| ------------- | ------------- | ------------- |
| `GET` | `/posts?title=json-mock&author=therebelrobot` | Return list of all posts with title === json-mock && author === therebelrobot |

#### Pagination

To paginate resources, add `_start` and `_end` or `_limit`. An `X-Total-Count` header is included in the response.

| Method  | Path | Description |
| ------------- | ------------- | ------------- |
| `GET` | `/posts?_start=20&_end=30` | Return list of posts starting with the 20th and ending with the 30th |
| `GET` | `/posts?_start=20&_limit=10` | Return list of posts starting with the 20th and limiting to 10 results |
| `GET` | `/posts/1/comments?_start=20&_end=30` | Return list of comments for post 1 starting with the 20th comment and ending with the 30th |

#### Sorting

To sort resources, add `_sort` and `_order` (ascending order by default).

| Method  | Path | Description |
| ------------- | ------------- | ------------- |
| `GET` | `/posts?_sort=author&_order=DESC` | Return all posts sorted by author, descending |
| `GET` | `/posts/1/comments?_sort=votes&_order=ASC` | Return all comments sorted by vote count, ascending |

#### Full Text Searching

To make a full-text search on resources, add `q`.

| Method  | Path | Description |
| ------------- | ------------- | ------------- |
| `GET` | `/posts?q=internet` | return all posts with "internet" in any field |


### Reserved Routes

Only two routes are reserved by the server: `/` and `/db`.

| Method  | Path | Description |
| ------------- | ------------- | ------------- |
| `GET` | `/` | Returns default index file or serves `./public` directory. Refer to "Static File Server" below. |
| `GET` | `/db` | Returns entire database |

## Additional Features

### Static File Server

You can use JSON Server to serve your HTML, JS and CSS, simply create a `./public` directory.

### CORS / JSONP

You can access your fake API from anywhere using CORS and JSONP.

### Remote Schemas

You can load remote schemas:

```bash
$ json-mock http://example.com/file.json
$ json-mock http://jsonplaceholder.typicode.com/db
```

### Dynamic Data

You can use normal javascript files to dynamically create data:

```javascript
module.exports = function () {
  data = { users: [] }
  // Create 1000 users
  for (var i = 0; i < 1000; i++) {
    data.users.push({ id: i, name: 'user' + i })
  }
  return data
}
```

```bash
$ json-mock index.js
```

### Programmatic Use

You can even use JSON Server programmatically in your applications:

```javascript
var jsonServer = require('json-mock')

var server = jsonServer.create()         // Express server
server.use(jsonServer.defaults)          // Default middlewares (logger, public, cors)
server.use(jsonServer.router('db.json')) // Express router

server.listen(3000)
```

For an in-memory database, you can pass an object to `jsonServer.route()`.

### Deployment

You can deploy JSON Server. I will be uploading an example deployment soon.

## Purpose

I forked this repo from [typicode/json-server](http://github.com/typicode/json-server). Typicode expressed [a desire to keep the json-server API simple](https://github.com/typicode/json-server/pull/82#issuecomment-101393288), which in my opinion restricts what can be done in mocking apis.

I want this module to server the needs of it's users. If you have a need for your mock server that isn't being met, please let me know by opening a Github issue.

## Roadmap

These are some features that are on my plate to add to json-mock. They will be added as time permits.

* Create online demo on Heroku
* `feross/standard` styling compliance
* Verify 100% code coverage
* Replace usage of `underscore` with `lodash`
* Add option to enable [json:api](http://jsonapi.org/) response formatting
* Remove orphaned items on `DELETE`

## Contributing

Contributing is more than welcome. The API is ever expanding, to allow for more advanced features. Before beginning, please review the guidelines below to maximize your efforts:

* Make sure you review the feross/standard styling and make sure your additions comply with it. 
* Before starting a large change, make sure you either open a github issue about it or find a current issue in regards to it,make sure we aren't duplicating efforts. 
* Fork the repo, make changes in your fork.
* Make sure any changes have an additional tests added for them in `./test`
* Make sure your new tests and all others pass when running `npm test`
* Open a pull request and describe your changes, outlining what was added, changed, fixed, and removed from the usage API.

Once I have an opportunity to review your code, if it enhances the capabilities of the lib, I'll get it merged in. Once merged I'll ask for info to add to the contributor list below and in `package.json`.

### Contributors

* Trent Oswald <a href="mailto:trentoswald@therebelrobot.com">trentoswald@therebelrobot.com</a> - [/therebelrobot](http://github.com/therebelrobot) - [therebelrobot.com](http://therebelrobot.com)

## Changelog

All notable changes to this project will be documented in this section.

*This project adheres to [Semantic Versioning](http://semver.org/) and [Keep A Changelog](http://keepachangelog.com/).*

### Unreleased

### v0.1.0 - Initial Release - 2015-05-13
#### Added

* README update
* Infinitely nested routing
* Coverage reports and test scripts

## License

[The MIT License (MIT)](https://tldrlegal.com/license/mit-license)

Copyright (c) 2015 [Trent Oswald (therebelrobot)](https://github.com/therebelrobot)

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

