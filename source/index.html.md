---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - shell

toc_footers:
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>
  - <a href='https://www.getpostman.com/collections/2a3ec089db2d9d917e7d'>Try Requests on Postman</a>

search: true
---

# Introduction

Welcome to the Hawkeye API! This is intended for internal use in Hawkeye clients, like our iOS app and website. Using the API, you can create tests, upload test data, and view test results.

In the dark area to the right, you'll find example requests and responses. 

This example API documentation page was created with [Slate](https://github.com/lord/slate). Please update the docs as you see fit.

# Authentication

## Overview

Hawkeye uses tokens to authenticate users. Before sending any requests, you must sign in to a user account. After creating an account or signing in, you'll be returned several authentication headers which should be included in subsequent requests.

## Create an Account

> To create an account, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/auth"
  -X POST
  -d email=authexample@usehawkeye.com
  -d password=examplepassword
  -d password_confirm=examplepassword
```

> The above request returns JSON structured like this:

```json
{
    "status": "success",
    "data": {
        "id": 1,
        "provider": "email",
        "uid": "authexample@usehawkeye.com",
        "allow_password_change": false,
        "first_name": "Auth",
        "last_name": "Example",
        "email": "authexample@usehawkeye.com",
        "created_at": "2019-06-16T06:00:29.523Z",
        "updated_at": "2019-06-16T06:00:29.780Z",
        "company_name": "Hawkeye Labs, Inc.",
        "company_size": 3,
        "company_industry": "Design Tools",
        "role": "Engineer",
        "free_trial_end_date": "2022-11-27T06:00:29.310Z"
    }
}
```
> In addition, these headers will be returned.

```json
{
    "Access-Token": "v8jZB-iMSwcJhIPTTqrYvg",
    "Token-Type": "Bearer",
    "Client": "OVwx7W72CqL8qFwLw_2Lkg",
    "Expiry": "1568617089",
    "Uid": "authexample@usehawkeye.com"
}
```
> These headers should be included in all subsequent requests.

This endpoint creates a new user account.

### HTTP Request

`POST https://hawkeye-staging.herokuapp.com/api/v1/auth`

### URL Parameters

Parameter | Description
--------- | -----------
email | The user's email
password | The user's password
password_confirm | Confirmation of the user's password
first_name | The user's first name
last_name | The user's last name
company_name | The name of the user's company
company_size | The size of the user's company
company_industry | The industry of the user's company
role | The role of the user at their company

<aside class="success">
Requests must include ALL the auth headers described above.
</aside>

## Sign In

> To sign in to an existing account, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/auth/sign_in"
  -X POST
  -d email=authexample@usehawkeye.com
  -d password=examplepassword
```

> The above request returns JSON structured like this:

```json
{
    "data": {
        "id": 1,
        "email": "authexample@usehawkeye.com",
        "provider": "email",
        "uid": "authexample@usehawkeye.com",
        "allow_password_change": false,
        "first_name": "Auth",
        "last_name": "Example",
        "company_name": "Hawkeye Labs, Inc.",
        "company_size": 3,
        "company_industry": "Design Tools",
        "role": "Engineer",
        "free_trial_end_date": "2022-11-27T06:11:34.320Z"
    }
}
```
> In addition, these headers will be returned.

```json
{
    "Access-Token": "v8jZB-iMSwcJhIPTTqrYvg",
    "Token-Type": "Bearer",
    "Client": "OVwx7W72CqL8qFwLw_2Lkg",
    "Expiry": "1568617089",
    "Uid": "authexample@usehawkeye.com"
}
```
> These headers should be included in all subsequent requests.

This endpoint signs the user into an existing account.

### HTTP Request

`POST https://hawkeye-staging.herokuapp.com/api/v1/auth/sign_in`

### URL Parameters

Parameter | Description
--------- | -----------
email | The user's email
password | The user's password

<aside class="success">
Requests must include ALL the auth headers described above.
</aside>

## Sign Out

> To sign out, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/auth/sign_out"
-X DELETE
```

This endpoint signs the user out of their account on the device associated with the reques's auth headers.

### HTTP Request

`DELETE https://hawkeye-staging.herokuapp.com/api/v1/auth/sign_out`

## Update an Account

> To update an existing user account, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/auth"
  -X PUT
  -d first_name=New
```

> The above request returns JSON structured like this, representing the updated user:

```json
{
    "data": {
        "id": 1,
        "email": "authexample@usehawkeye.com",
        "provider": "email",
        "uid": "authexample@usehawkeye.com",
        "allow_password_change": false,
        "first_name": "New",
        "last_name": "Example",
        "company_name": "Hawkeye Labs, Inc.",
        "company_size": 3,
        "company_industry": "Design Tools",
        "role": "Engineer",
        "free_trial_end_date": "2022-11-27T06:11:34.320Z"
    }
}
```
> In addition, these headers will be returned.

This endpoint updates the account details for the currently-authenticated user.

### HTTP Request

`PUT https://hawkeye-staging.herokuapp.com/api/v1/auth`

### URL Parameters

Include any fields on the user object that you wish to update. Fields not included will remain unchanged.

Parameter | Description
--------- | -----------
email | The user's email
password | The user's password
password_confirm | Confirmation of the user's password
first_name | The user's first name
last_name | The user's last name
company_name | The name of the user's company
company_size | The size of the user's company
company_industry | The industry of the user's company
role | The role of the user at their company

## Implementation Details

We use the [Devise Token Auth](https://www.github.com/lynndylanhurley/devise_token_auth) gem to manage user account. You can find the docs [here](https://devise-token-auth.gitbook.io/devise-token-auth/usage). 

# Projects

Projects are used to organize tests conducted using Hawkeye. Each project should be used to test a single piece of content. For each project, multiple tests can be conducted, each represented by a session.

## Create a Project

> To create a project, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/projects"
-X POST
-d title="Example Project"
-d content_type=website
```

> The above request returns JSON structured like this:

```json
{
    "id": 200,
    "title": "Example Project",
    "content_type": "website",
    "url": null,
    "thumbnail": "/rails/active_storage/blobs/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBcjBTIiwiZXhwIjpudWxsLCJwdXIiOiJibG9iX2lkIn19--ec833985f133b834eacba621df44b41341eacbe0/instagram_thumb.png",
    "thumbnail_metadata": {
        "identified": true,
        "width": 110,
        "height": 190,
        "analyzed": true
    },
    "image": null,
    "sessions": []
}
```

This endpoint creates a new project. You can either create a project on its own or include an array of sessions using the `sessions_attributes` param.

### HTTP Request

`POST https://hawkeye-staging.herokuapp.com/api/v1/projects`

### URL Parameters

Parameter | Description
--------- | -----------
title | The title of the project
content_type | The type of content being tested. Website, app, or image.
thumbnail | Thumbnail data (using form data)
sessions_attributes | Array of sessions

## Get All Projects

> To get all projects, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/projects"
  -H "Access-Token: v8jZB-iMSwcJhIPTTqrYvg"
  -H "Token-Type: Bearer"
  -H "Client: OVwx7W72CqL8qFwLw_2Lkg"
  -H "Expiry: 1568617089"
  -H "Uid: authexample@usehawkeye.com"
```

> The above request returns JSON structured like this:

```json
[
    {
        "id": 201,
        "title": "Example Project 2",
        "content_type": "website",
        "url": null,
        "thumbnail":
        "/rails/active_storage/blobs/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBcjRTIiwiZXhwIjpudWxsLCJwdXIiOiJibG9iX2lkIn19--38e71a3c01c7aff1d44fb80a5be3a023625845ee/instagram_thumb.png",
        "thumbnail_metadata": {
            "identified": true,
            "width": 110,
            "height": 190,
            "analyzed": true
        },
        "image": null
    },
    {
        "id": 200,
        "title": "Example Project",
        "content_type": "website",
        "url": null,
        "thumbnail": "/rails/active_storage/blobs/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBcjBTIiwiZXhwIjpudWxsLCJwdXIiOiJibG9iX2lkIn19--ec833985f133b834eacba621df44b41341eacbe0/instagram_thumb.png",
        "thumbnail_metadata": {
            "identified": true,
            "width": 110,
            "height": 190,
            "analyzed": true
        },
        "image": null
    }
]
```

This endpoint retrieves all projects for the current user. By default, this will include sessions for each project. Set the `include_sessions` parameter to `false` to fetch only the projects themselves.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/projects`

### URL Parameters

Parameter | Description
--------- | -----------
shallow_sessions | Whether sessions should be included for each project. Defaults to false, meaning sessions will be included.

# Sessions

A session represents a single test conducted using Hawkeye. Each session can contain multiple pages (which are used to generate heatmaps) and a screen recording.

## Create a Session

> To create a session, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>/sessions"
-X POST
-d title="Example Session"
-d content_type="website"
-d started_at="2019-06-13'0'10:12:12.000Z"
-d ended_at="2019-06-13'0'10:20:12.000Z"
-d screen_width=200.0
-d screen_height=600.0
-d favorite=false
-d tester[email]=tester@usehawkeye.com
-d tester[first_name]=Tester
-d tester[last_name]=Smith
```

> The above request returns JSON structured like this:

```json
{
    "id": 1013,
    "title": "Example Session",
    "content_type": "website",
    "started_at": "2019-06-13T10:12:12.000Z",
    "ended_at": "2019-06-13T10:20:12.000Z",
    "favorite": false,
    "thumbnail": null,
    "thumbnail_metadata": null,
    "video": null,
    "screen_width": 200,
    "screen_height": 600,
    "tester": {
        "id": 23,
        "first_name": "Tester",
        "last_name": "Smith",
        "email": "tester@usehawkeye.com"
    }
}
```

This endpoint creates a new session for a given project. All pages visited during the test, complete with eye position and touch events for each page, should be included in this request. 

### HTTP Request

`POST https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>/sessions`

### URL Parameters

Parameter | Description
--------- | -----------
project_id | The project the session belongs to
title | The title of the session
content_type | The type of content being tested. Website, app, or image.
thumbnail | Thumbnail data (using form data)
started_at | The time the test began
ended_at | The time the test ended
screen_width | The width of the device
screen_height | The height of the device
favorite | Whether or not the test is favorited
tester | Dictionary containing the tester's info (first_name, last_name, email, etc.)
invite_token | Token identifier of the invitation to participate in this test
video | Screen recording video data (using form data)
pages_attributes | Array of pages visited during the test

## Get All Sessions

> To get all sessions for a project, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>/sessions"
-X GET
```

> The above request returns JSON structured like this:

```json
[
    {
        "id": 1013,
        "title": "Example Session",
        "content_type": "website",
        "started_at": "2019-06-13T10:12:12.000Z",
        "ended_at": "2019-06-13T10:20:12.000Z",
        "favorite": false,
        "thumbnail": null,
        "thumbnail_metadata": null,
        "video": null,
        "screen_width": 200,
        "screen_height": 600,
        "tester": {
            "id": 23,
            "first_name": "Tester",
            "last_name": "Smith",
            "email": "tester@usehawkeye.com"
        }
    },
    {
        "id": 1012,
        "title": "Example Session",
        "content_type": "website",
        "started_at": "2019-06-13T10:12:12.000Z",
        "ended_at": "2019-06-13T10:20:12.000Z",
        "favorite": false,
        "thumbnail": null,
        "thumbnail_metadata": null,
        "video": null,
        "screen_width": 200,
        "screen_height": 600,
        "tester": null
    }
]
```
> Note: No page data is returned.

This endpoint gets all sessions for a given project. This does not include page data. To fetch the complete data for a session, including page metadata, see [Get an Individual Session](#get-an-individual-session)

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>/sessions`

### URL Parameters

Parameter | Description
--------- | -----------
project_id | The project the session belongs to

## Get an Individual Session

> To get an individual session, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/sessions/<session_id>"
-X GET
-d shallow_pages=true
```

> The above request returns JSON structured like this:

```json
{
    "id": 1016,
    "title": "Example Session",
    "content_type": "website",
    "started_at": "2019-06-13T10:12:12.000Z",
    "ended_at": "2019-06-13T10:20:12.000Z",
    "favorite": false,
    "thumbnail": null,
    "thumbnail_metadata": null,
    "video": null,
    "screen_width": 200,
    "screen_height": 600,
    "pages": [
        {
            "id": 2590,
            "title": "Test 2",
            "started_at": "2019-06-13T10:12:12.000Z",
            "ended_at": "2019-06-13T10:20:12.000Z",
            "width": 200,
            "height": 600,
            "scrollable": true,
            "thumbnail": null,
            "thumbnail_metadata": null
        },
        {
            "id": 2589,
            "title": "Test 1",
            "started_at": "2019-06-13T10:12:12.000Z",
            "ended_at": "2019-06-13T10:20:12.000Z",
            "width": 200,
            "height": 600,
            "scrollable": true,
            "thumbnail": null,
            "thumbnail_metadata": null
        }
    ],
    "tester": {
        "id": 23,
        "first_name": "Tester",
        "last_name": "Smith",
        "email": "tester@usehawkeye.com"
    }
}
```

This endpoint gets an individual session. 

By default, complete page data will be included. If you'd like to get back thumbnails and tiltes for each page without eye positions and touch event data, set the `shallow_pages` to `true`. To fetch the complete data for an individual page, see [Get an Individual Page](#get-an-individual-page) and to fetch the complete data for all pages in a session, see [Get All Pages](#get-all-pages).

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/sessions/<session_id>`

### URL Parameters

Parameter | Description
--------- | -----------
session_id | The identifier of the session
shallow_pages | Only returns metadata for sessions. Defaults to false, meaning full session data will be returned.

## Update a Session

> To update a session, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/sessions/<session_id>"
-X PUT
-d title="New Title"
```

> The above request returns JSON structured like this:

```json
{
    "id": 1012,
    "title": "New Title",
    "content_type": "website",
    "started_at": "2019-06-13T10:12:12.000Z",
    "ended_at": "2019-06-13T10:20:12.000Z",
    "favorite": false,
    "thumbnail": null,
    "thumbnail_metadata": null,
    "video": null,
    "screen_width": 200,
    "screen_height": 600,
    "tester": null
}
```

This endpoint updates a session.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/sessions/<session_id>`

### URL Parameters

Parameter | Description
--------- | -----------
session_id | The identifier of the session
title | The title of the session
favorite | Whether or not the test is favorited

## Delete a Session

> To delete a session, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/sessions/<session_id>"
-X DELETE
```

This endpoint deletes a session.

### HTTP Request

`DELETE https://hawkeye-staging.herokuapp.com/api/v1/sessions/<session_id>`

### URL Parameters

Parameter | Description
--------- | -----------
session_id | The identifier of the session

# Pages

Data is recorded for each page the user visits during a test, including their eye positions and touch events. This data is used to generate heatmaps and focus point diagrams.

## Get All Pages

> To get the complete data for all pages in a session, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/sessions/<session_id>/pages"
-X GET
```

> The above request returns JSON structured like this:

```json
[
    {
        "id": 2590,
        "title": "Test 2",
        "started_at": "2019-06-13T10:12:12.000Z",
        "ended_at": "2019-06-13T10:20:12.000Z",
        "width": 200,
        "height": 600,
        "scrollable": true,
        "thumbnail": null,
        "thumbnail_metadata": null,
        "screen_capture": null,
        "url": null,
        "gestures": [],
        "eye_positions": []
    },
    {
        "id": 2589,
        "title": "Test 1",
        "started_at": "2019-06-13T10:12:12.000Z",
        "ended_at": "2019-06-13T10:20:12.000Z",
        "width": 200,
        "height": 600,
        "scrollable": true,
        "thumbnail": null,
        "thumbnail_metadata": null,
        "screen_capture": null,
        "url": null,
        "gestures": [
            {
                "id": 16862,
                "started_at": "2019-06-13T10:12:12.000Z",
                "ended_at": "2019-06-13T10:12:14.000Z",
                "touches": [
                    {
                        "id": 297201,
                        "timestamp": "2019-06-13T10:12:12.000Z",
                        "x_pos": 10,
                        "y_pos": 20
                    }
                ]
            }
        ],
        "eye_positions": [
            {
                "id": 279686,
                "x_pos": 10,
                "y_pos": 2000,
                "started_at": "2019-06-13T10:12:12.000Z",
                "ended_at": "2019-06-13T10:12:12.000Z",
                "abs_x_pos": 10,
                "abs_y_pos": 14
            }
        ]
    }
]
```

This endpoint gets all complete data for all pages in a session, including the eye positions and touch events recorded during the test. To fetch page metadata without the complete data, see [Get an Individual Session](#get-an-individual-session). 

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/sessions/<session_id>/pages`

### URL Parameters

Parameter | Description
--------- | -----------
session_id | The identifier of the session that the pages belong to

## Get an Individual Page

> To get an individual page, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/pages/<page_id>"
-X GET
```

> The above request returns JSON structured like this:

```json
{
    "id": 2589,
    "title": "Test 1",
    "started_at": "2019-06-13T10:12:12.000Z",
    "ended_at": "2019-06-13T10:20:12.000Z",
    "width": 200,
    "height": 600,
    "scrollable": true,
    "thumbnail": null,
    "thumbnail_metadata": null,
    "screen_capture": null,
    "url": null,
    "gestures": [
        {
            "id": 16862,
            "started_at": "2019-06-13T10:12:12.000Z",
            "ended_at": "2019-06-13T10:12:14.000Z",
            "touches": [
                {
                    "id": 297201,
                    "timestamp": "2019-06-13T10:12:12.000Z",
                    "x_pos": 10,
                    "y_pos": 20
                }
            ]
        }
    ],
    "eye_positions": [
        {
            "id": 279686,
            "x_pos": 10,
            "y_pos": 2000,
            "started_at": "2019-06-13T10:12:12.000Z",
            "ended_at": "2019-06-13T10:12:12.000Z",
            "abs_x_pos": 10,
            "abs_y_pos": 14
        }
    ]
}
```

This endpoint gets an individual page, including the eye positions and touch events recorded during the page visit.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/pages/<page_id>`

### URL Parameters

Parameter | Description
--------- | -----------
page_id | The identifier of the page
