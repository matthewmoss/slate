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

## Get an Individual project

> To get an individual project, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>"
-H "Access-Token: v8jZB-iMSwcJhIPTTqrYvg"
-H "Token-Type: Bearer"
-H "Client: OVwx7W72CqL8qFwLw_2Lkg"
-H "Expiry: 1568617089"
-H "Uid: authexample@usehawkeye.com"
```

> The above request returns JSON structured like this:

```json
{
    "id": 16,
    "title": "Example Project 2",
    "content_type": "website",
    "url": null,
    "thumbnail": "/rails/active_storage/blobs/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBDUT09IiwiZXhwIjpudWxsLCJwdXIiOiJibG9iX2lkIn19--a8dfb753e249df0d27fed026312cb018f7f5b2a3/instagram_thumb.png",
    "thumbnail_metadata": {
        "identified": true
    },
    "image": null,
    "instructions": null,
    "welcome_message": null,
    "exit_message": null,
    "exit_url": null,
    "exit_title": null,
    "eye_tracking_type": "optional",
    "screen_recording_enabled": true,
    "audio_enabled": true,
    "hide_visualizations": true,
    "prompt_user_info": true,
    "sessions_count": 26,
    "sessions": [
        {
            "id": 269,
            "state": null,
            "title": null,
            "content_type": null,
            "started_at": null,
            "ended_at": null,
            "favorite": false,
            "thumbnail": null,
            "thumbnail_metadata": null,
            "video": null,
            "screen_width": null,
            "screen_height": null,
            "pages_count": 0,
            "visited": false,
            "created_at": "2019-08-14T05:40:04.954Z",
            "updated_at": "2019-08-14T05:40:04.954Z",
            "device_model": null,
            "device_type": null,
            "user": {
                "id": 3,
                "first_name": null,
                "last_name": null,
                "avatar": null
            },
            "tester": {
                "id": 17,
                "first_name": "Matt",
                "last_name": "Moss",
                "email": "matthewmoss@me.com",
                "sessions_count": 12,
                "first_test_at": "2019-07-12T17:41:22.194Z",
                "last_test_at": "2019-08-14T05:40:04.949Z",
                "creator": null,
                "avatar": null
            }
        },
        {
            "id": 268,
            "state": null,
            "title": null,
            "content_type": null,
            "started_at": null,
            "ended_at": null,
            "favorite": false,
            "thumbnail": null,
            "thumbnail_metadata": null,
            "video": null,
            "screen_width": null,
            "screen_height": null,
            "pages_count": 0,
            "visited": false,
            "created_at": "2019-08-14T05:38:30.447Z",
            "updated_at": "2019-08-14T05:38:30.447Z",
            "device_model": null,
            "device_type": null,
            "user": {
                "id": 2,
                "first_name": null,
                "last_name": null,
                "avatar": null
            },
            "tester": {
                "id": 17,
                "first_name": "Matt",
                "last_name": "Moss",
                "email": "matthewmoss@me.com",
                "sessions_count": 12,
                "first_test_at": "2019-07-12T17:41:22.194Z",
                "last_test_at": "2019-08-14T05:40:04.949Z",
                "creator": null,
                "avatar": null
            }
        }
    ],
    "public_invite": {
        "id": 51,
        "token": "uXytinYZWFx64iyUbCuE",
        "url": "/session_invites/uXytinYZWFx64iyUbCuE"
    }
}
```

This endpoint retrieves an individual project, including it's sessions.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>`

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
        "visited": true,
        "tester": {
            "id": 23,
            "first_name": "Tester",
            "last_name": "Smith",
            "email": "tester@usehawkeye.com"
        }
    },
    {
        "id": 1012,
        "visited": false,
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
    "visited": true,
    "visitors": [
        {
            "id": 17,
            "first_name": "Matt",
            "last_name": "Moss",
            "avatar": null
        }
    ],
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
    "events": [
        {
            "id": 5,
            "started_at": "2019-10-22T18:11:05.103Z",
            "ended_at": "2019-10-22T18:11:21.520Z",
            "eventable_type": "Page",
            "relative_started_at": 0.001,
            "relative_ended_at": 16.418,
            "duration": 16.417,
            "duration_percent": 0.9798269173381082,
            "started_at_percent": 5.96836765144733e-05,
            "ended_at_percent": 0.9798866010146225,
            "eventable": {
                "id": 4276,
                "token": "RiOfyEijCRPWQUu-Kb9wHg",
                "state": "completed",
                "title": "Online payment processing for internet businesses - Stripe",
                "started_at": "2019-10-22T18:11:05.103Z",
                "ended_at": "2019-10-22T18:11:21.520Z",
                "width": 375.0,
                "height": 812.0,
                "scrollable": true,
                "thumbnail": "/rails/active_storage/blobs/eyJfcmFpbHMiOnsibWVzc2FnZSI6IkJBaHBBbEVtIiwiZXhwIjpudWxsLCJwdXIiOiJibG9iX2lkIn19--ca334b0a9888393fb5e94db60706ee20dde6ba29/thumbnail.png",
                "thumbnail_metadata": {
                    "identified": true,
                    "width": 231,
                    "height": 500,
                    "analyzed": true
                }
            }
        },
        {
            "id": 1,
            "started_at": "2019-10-22T18:11:12.997Z",
            "ended_at": "2019-10-22T18:11:13.303Z",
            "eventable_type": "Gesture",
            "relative_started_at": 7.895,
            "relative_ended_at": 8.201,
            "duration": 0.306,
            "duration_percent": 0.01826320501342883,
            "started_at_percent": 0.47120262608176666,
            "ended_at_percent": 0.48946583109519554,
            "eventable": {
                "id": 20114,
                "started_at": "2019-10-22T18:11:12.997Z",
                "ended_at": "2019-10-22T18:11:13.303Z",
                "is_tap": false
            }
        },
        {
            "id": 2,
            "started_at": "2019-10-22T18:11:14.155Z",
            "ended_at": "2019-10-22T18:11:14.287Z",
            "eventable_type": "Gesture",
            "relative_started_at": 9.053,
            "relative_ended_at": 9.185,
            "duration": 0.132,
            "duration_percent": 0.007878245299910476,
            "started_at_percent": 0.5403163234855268,
            "ended_at_percent": 0.5481945687854373,
            "eventable": {
                "id": 20115,
                "started_at": "2019-10-22T18:11:14.155Z",
                "ended_at": "2019-10-22T18:11:14.287Z",
                "is_tap": false
            }
        },
        {
            "id": 3,
            "started_at": "2019-10-22T18:11:14.487Z",
            "ended_at": "2019-10-22T18:11:14.912Z",
            "eventable_type": "Gesture",
            "relative_started_at": 9.385,
            "relative_ended_at": 9.81,
            "duration": 0.425,
            "duration_percent": 0.02536556251865115,
            "started_at_percent": 0.5601313040883319,
            "ended_at_percent": 0.5854968666069831,
            "eventable": {
                "id": 20116,
                "started_at": "2019-10-22T18:11:14.487Z",
                "ended_at": "2019-10-22T18:11:14.912Z",
                "is_tap": false
            }
        },
        {
            "id": 4,
            "started_at": "2019-10-22T18:11:19.605Z",
            "ended_at": "2019-10-22T18:11:19.740Z",
            "eventable_type": "Gesture",
            "relative_started_at": 14.503,
            "relative_ended_at": 14.638,
            "duration": 0.135,
            "duration_percent": 0.008057296329453895,
            "started_at_percent": 0.8655923604894062,
            "ended_at_percent": 0.8736496568188601,
            "eventable": {
                "id": 20117,
                "started_at": "2019-10-22T18:11:19.605Z",
                "ended_at": "2019-10-22T18:11:19.740Z",
                "is_tap": true
            }
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

# Testers

## Get All Testers

> To get all testers, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/testers/segments/all"
-X GET
```

> The above request returns JSON structured like this:

```json
{
    "id": null,
    "name": "All",
    "uid": "all",
    "testers": [
        {
            "id": 27,
            "first_name": "Tester",
            "last_name": "Smith",
            "email": "tester@usehawkeye.com",
            "sessions_count": 1,
            "first_test_at": "2019-06-23T22:35:05.961Z",
            "last_test_at": "2019-06-23T22:35:05.961Z",
            "creator": {
                "id": 68,
                "first_name": "Matt",
                "last_name": "Moss"
            },
            "avatar": null
        },
        {
            "id": 28,
            "first_name": "Jim",
            "last_name": "Smith",
            "email": "example@usehawkeye.com",
            "sessions_count": 0,
            "first_test_at": null,
            "last_test_at": null,
            "creator": {
                "id": 68,
                "first_name": "Matt",
                "last_name": "Moss"
            },
            "avatar": null
        }
    ]
}
```
This endpoint fetches all testers.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/testers/segments/all`

### URL Parameters

Parameter | Description
--------- | -----------
segment_id | The identifier of the segment

## Get an Individual Tester

> To get an individual testers, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/testers/<tester_id>"
-X GET
```

> The above request returns JSON structured like this:

```json
{
    "id": 27,
    "first_name": "Tester",
    "last_name": "Smith",
    "email": "tester@usehawkeye.com",
    "sessions_count": 1,
    "first_test_at": "2019-06-23T22:35:05.961Z",
    "last_test_at": "2019-06-23T22:35:05.961Z",
    "creator": {
        "id": 69,
        "first_name": "Matt",
        "last_name": "Moss"
    },
    "avatar": null,
    "tester_segments": [
        {
            "id": 2,
            "name": "Example",
            "uid": "MQWPqJ_y188pCcJxNcSz"
        }
    ],
    "sessions": [
        {
            "id": 1101,
            "title": "Example Session",
            "content_type": "website",
            "started_at": "2019-06-13T10:12:12.000Z",
            "ended_at": "2019-06-13T10:20:12.000Z",
            "favorite": false,
            "thumbnail": null,
            "thumbnail_metadata": null,
            "video": null,
            "screen_width": 200,
            "screen_height": 600
        }
    ],
    "session_invites": []
}
```
This endpoint fetches an individual tester, including all of the sessions they have completed.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/testers/<tester_id>`

### URL Parameters

Parameter | Description
--------- | -----------
tester_id | The identifier of the tester

## Create a Tester

> To create a tester, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/testers/"
-X POST
-d first_name=Jim
-d last_name=Smith
-d email=example@usehawkeye.com
-d tester_segment_ids[]=1
```

> The above request returns JSON structured like this:

```json
{
    "id": 29,
    "first_name": "Jim",
    "last_name": "Smith",
    "email": "exafmsfsfple@usehawkeye.com",
    "sessions_count": 0,
    "first_test_at": null,
    "last_test_at": null,
    "creator": {
        "id": 69,
        "first_name": null,
        "last_name": null
    },
    "avatar": null,
    "tester_segments": [
        {
            "id": 1,
            "name": "Example",
            "uid": "uzqvg-p9TAGUPkdWy43Q"
        }
    ],
    "sessions": [],
    "session_invites": []
}
```
This endpoint creates a new tester. The tester can be added to several segments by passing an array of tester segment ids to the `tester_segment_ids` parameter. 

### HTTP Request

`POST https://hawkeye-staging.herokuapp.com/api/v1/testers`

### URL Parameters

Parameter | Description
--------- | -----------
first_name | The tester's first_name
last_name | The tester's last_name
email | The tester's email
tester_segment_ids[] | An array of tester segment ids

## Update a Tester

> To update a tester, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/testers/29"
-X POST
-d first_name=New
-d last_name=Smith
-d email=example@usehawkeye.com
-d tester_segment_ids[]=1
```

> The above request returns JSON structured like this:

```json
{
    "id": 29,
    "first_name": "New",
    "last_name": "Smith",
    "email": "exafmsfsfple@usehawkeye.com",
    "sessions_count": 0,
    "first_test_at": null,
    "last_test_at": null,
    "creator": {
        "id": 69,
        "first_name": null,
        "last_name": null
    },
    "avatar": null,
    "tester_segments": [
        {
            "id": 1,
            "name": "Example",
            "uid": "uzqvg-p9TAGUPkdWy43Q"
        }
    ],
    "sessions": [],
    "session_invites": []
}
```
This endpoint updates a tester. Any segment ids passed to the  `tester_segment_ids` parameter will override previous segment ids, not append to them.

### HTTP Request

`PUT https://hawkeye-staging.herokuapp.com/api/v1/testers/<tester_id>`

### URL Parameters

Parameter | Description
--------- | -----------
tester_id | The identifier of the tester
first_name | The tester's first_name
last_name | The tester's last_name
email | The tester's email
tester_segment_ids[] | An array of tester segment ids

## Delete a Tester

> To delete a tester, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/testers/29"
-X DELETE
```
This endpoint deletes a tester.

### HTTP Request

`DELETE https://hawkeye-staging.herokuapp.com/api/v1/testers/29`

### URL Parameters

Parameter | Description
--------- | -----------
tester_id | The identifier of the tester

# Tester Segmentation
A tester segment represents a group of testers, making it easy for users to invite or contact a large group of testers all at once. 

## Get All Tester Segments

> To get all testers segments, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/testers/segments"
-X GET
```

> The above request returns JSON structured like this:

```json
[
    {
        "id": 1,
        "name": "Example",
        "uid": "uzqvg-p9TAGUPkdWy43Q"
    },
    {
        "id": 2,
        "name": "Example",
        "uid": "MQWPqJ_y188pCcJxNcSz"
    }
]
```
This endpoint fetches a list of all segments.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/testers/segments`

## Get All Testers in Segment

> To get all testers in a segment, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/testers/segments/<segment_uid>"
-X GET
```

> The above request returns JSON structured like this:

```json
{
    "id": 2,
    "name": "Example",
    "uid": "MQWPqJ_y188pCcJxNcSz",
    "testers": [
        {
            "id": 27,
            "first_name": "Tester",
            "last_name": "Smith",
            "email": "tester@usehawkeye.com",
            "sessions_count": 1,
            "first_test_at": "2019-06-23T22:35:05.961Z",
            "last_test_at": "2019-06-23T22:35:05.961Z",
            "creator": {
                "id": 68,
                "first_name": "Matt",
                "last_name": "Matt"
            },
            "avatar": null
        },
        {
            "id": 29,
            "first_name": "New",
            "last_name": "Smith",
            "email": "example@usehawkeye.com",
            "sessions_count": 0,
            "first_test_at": null,
            "last_test_at": null,
            "creator": {
                "id": 68,
                "first_name": "Matt",
                "last_name": "Matt"
            },
            "avatar": null
        }
    ]
}
```
This endpoint fetches all testers in a specific segment. NOTE: The url should end with the segment's uid, not its id.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/testers/segments/<segment_uid>`

### URL Parameters

Parameter | Description
--------- | -----------
segment_uid | The uid of the segment

## Create a Tester Segment

> To create a tester segment, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/testers/segments"
-X POST
-d name=Example
-d tester_ids[]=27
```

> The above request returns JSON structured like this:

```json
{
    "id": 2,
    "name": "Example",
    "uid": "MQWPqJ_y188pCcJxNcSz",
    "testers": [
        {
            "id": 27,
            "first_name": "Tester",
            "last_name": "Smith",
            "email": "tester@usehawkeye.com",
            "sessions_count": 1,
            "first_test_at": "2019-06-23T22:35:05.961Z",
            "last_test_at": "2019-06-23T22:35:05.961Z",
            "creator": {
                "id": 69,
                "first_name": "Matt",
                "last_name": "Moss"
            },
            "avatar": null
        }
    ]
}
```
This endpoint creates a new tester segment. To include a group of testers in the segment, pass an array of tester ids to the `tester_ids` parameter.

### HTTP Request

`POST https://hawkeye-staging.herokuapp.com/api/v1/testers/segments`

### URL Parameters

Parameter | Description
--------- | -----------
name | The name of the segment
tester_ids | The testers to included in the segment

## Update a Tester Segment

> To update a test segment, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/testers/segments/<segment_uid>"
-X PUT
-d name=New
-d tester_ids[]=29
```

> The above request returns JSON structured like this:

```json
{
    "id": 2,
    "name": "New",
    "uid": "MQWPqJ_y188pCcJxNcSz",
    "testers": [
        {
            "id": 27,
            "first_name": "Tester",
            "last_name": "Smith",
            "email": "tester@usehawkeye.com",
            "sessions_count": 1,
            "first_test_at": "2019-06-23T22:35:05.961Z",
            "last_test_at": "2019-06-23T22:35:05.961Z",
            "creator": {
                "id": 68,
                "first_name": "Matt",
                "last_name": "Moss"
            },
            "avatar": null
        },
        {
            "id": 29,
            "first_name": "New",
            "last_name": "Smith",
            "email": "example@usehawkeye.com",
            "sessions_count": 0,
            "first_test_at": null,
            "last_test_at": null,
            "creator": {
                "id": 68,
                "first_name": "Matt",
                "last_name": "Moss"
            },
            "avatar": null
        }
    ]
}
```
This endpoint updates a tester segment. To append testers to the segment, pass an array of tester ids to the `append_tester_ids` parameter. To remove testers from the segment, pass an array of tester ids to  `remove_tester_ids`. To set all testers that are part of the segment, use the `tester_ids` parameter. 

### HTTP Request

`PUT https://hawkeye-staging.herokuapp.com/api/v1/testers/segments/<segment_uid>`

### URL Parameters

Parameter | Description
--------- | -----------
segment_uid | The uid of the segment
name | The name of the segment
tester_ids | Array of tester ids to override existing testers
append_tester_ids | Array of tester ids to append to existing testers
remove_tester_ids | Array of testers to remove from existing testers

# Favorites

## Get All Favorites

> To get all favorites, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/favorites"
-X GET
```

> The above request returns JSON structured like this:

```json
[
    {
        "id": 1101,
        "title": "Example Session",
        "content_type": "website",
        "started_at": "2019-06-13T10:12:12.000Z",
        "ended_at": "2019-06-13T10:20:12.000Z",
        "favorite": true,
        "thumbnail": null,
        "thumbnail_metadata": null,
        "video": null,
        "screen_width": 200,
        "screen_height": 600,
        "tester": {
            "id": 27,
            "first_name": "Tester",
            "last_name": "Smith",
            "email": "tester@usehawkeye.com",
            "sessions_count": 1,
            "first_test_at": "2019-06-23T22:35:05.961Z",
            "last_test_at": "2019-06-23T22:35:05.961Z",
            "creator": {
                "id": 69,
                "first_name": null,
                "last_name": null
            },
            "avatar": null
        }
    }
]
```
This endpoint fetches favorited sessions across all projects in the organization.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/favorites`

# Session Invites

## Send Session Invigations

> To send session invitations, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>/session_invites"
-X POST
-d name=Example
-d testers[][email]=matthewmoss@me.com
-d testers[][first_name]=Matt
-d testers[][last_name]=Moss
-d testers[][email]=sdfsdf@me.com
```

> The above request returns JSON structured like this:

```json
[
    {
        "id": 66,
        "token": "83dEgUXwcTZm6Rx_44tx",
        "url": "/session_invites/83dEgUXwcTZm6Rx_44tx"
    },
    {
        "id": 67,
        "token": "_ed5TncnvyJSWEQtVSuk",
        "url": "/session_invites/_ed5TncnvyJSWEQtVSuk"
    }
]
```
This endpoint sends email invitations to a group of testers.

### HTTP Request

`POST https://hawkeye-staging.herokuapp.com/api/v1/testers/segments`

### URL Parameters

Parameter | Description
--------- | -----------
project_id | The project testers are being invited to.
name | An optional name for the invitation.
testers | Array of testers to be invited.
testers[][email] | Email address of a tester
testers[][first_name] | First name of a tester
testers[][last_name] | Last name of a tester

# Notes

Notes let users record user behavior.  They can be attached to a specific timestamp in a session using an `Event` or pinned to a point on heatmap using a `Pin` (coming soon).

## Create a Timestamped Note

> To create a note, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/notes"
-X POST
-d content="Test"
-d session_id=1
-d started_at="2019-06-13'0'10:12:12.000Z"
-d ended_at="2019-06-13'0'10:20:12.000Z"
```

> The above request returns JSON structured like this:

```json
{
    "id": 26,
    "started_at": "2019-06-13T10:12:12.000Z",
    "ended_at": "2019-06-13T10:12:12.000Z",
    "eventable_type": "Note",
    "relative_started_at": 0.0,
    "relative_ended_at": 0.0,
    "duration": 0.0,
    "duration_percent": 0.0,
    "started_at_percent": 0.0,
    "ended_at_percent": 0.0,
    "eventable": {
        "id": 16,
        "content": "Test",
        "resolved": false,
        "upvotes": 0,
        "user": {
            "id": 2,
            "first_name": "Matt",
            "last_name": "Moss",
            "avatar": null
        }
    }
}
```

This endpoint creates a note at a specific timestamp in a session. The response will include the full `Event` with the `Note` included under parameter `eventable`.

### HTTP Request

`POST https://hawkeye-staging.herokuapp.com/api/v1/notes`

### URL Parameters

Parameter | Description
--------- | -----------
note_id | The id of the note
content | The content of the note.
session_id | The ID of the session to attach the note to.
started_at | The time in the session where the note began.
ended_at | The time in the session where the note ended. For now, should be the same as started_at.
resolved | Whether or not the note is resolved.

## Update a Note

> To update a note, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/notes/<note_id>"
-X PUT
-d content="Test"
-d resolved=true
```

> The above request returns JSON structured like this:

```json
{
    "id": 8,
    "content": "Just updated this note!",
    "resolved": true,
    "upvotes": 0,
    "user": {
        "id": 2,
        "first_name": null,
        "last_name": null,
        "avatar": null
    },
    "replies": []
}
```

This endpoint updates a note. Event-specific fields like `session_id`,  `started_at`,  `ended_at` cannot be changed. This means you cannot update the timestamp of a note or the session it's attached to.

### HTTP Request

`PUT https://hawkeye-staging.herokuapp.com/api/v1/notes/<note_id>`

### URL Parameters

Parameter | Description
--------- | -----------
note_id | The id of the note
content | The content of the note.
resolved | Whether or not the note is resolved.

## Delete a Note

> To delete a note, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/notes/<note_id>"
-X DELETE
```

This endpoint deletes a note

### HTTP Request

`DELETE https://hawkeye-staging.herokuapp.com/api/v1/notes/<note_id>`

### URL Parameters

Parameter | Description
--------- | -----------
note_id | The id of the note

## Get All Notes for a Project

> To get all notes for a specific project, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>/notes"
-X GET
```

> The above request returns JSON structured like this:

```json
[
    {
        "id": 1,
        "content": "Testing",
        "resolved": false,
        "upvotes": 0,
        "user": {
            "id": 2,
            "first_name": "Matt",
            "last_name": "Moss",
            "avatar": null
        },
        "replies": []
    },
    {
        "id": 2,
        "content": "Cool",
        "resolved": false,
        "upvotes": 2,
        "user": {
            "id": 2,
            "first_name": "Matt",
            "last_name": "Moss",
            "avatar": null
        },
        "replies": []
    },
    {
        "id": 3,
        "content": "Working",
        "resolved": true,
        "upvotes": 3,
        "user": {
            "id": 2,
            "first_name": "Matt",
            "last_name": "Moss",
            "avatar": null
        },
        "replies": []
    }
]
```

This endpoint gets all notes for a specific project.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>/notes`

### URL Parameters

Parameter | Description
--------- | -----------
project_id | The id of the project

# Aggregated Heatmaps
Hawkeye creates aggregated look, tap, and scroll heatmaps for each page in a product. Heatmaps are segmented by device type. Each page in a product is represented by an `AggregatedPageSnapshot`, which has an `AggregatedPage` for each device type. For instance, to view the phone heatmap for the profile page in a project, the following could be done: `profileSnapshot.phone_page`.  Each `AggregatedPage` contains a `PageElementMap`, which contains all info needed to display a heatmap (screen size, scroll size, UI element locations, look data, tap data, etc.)

The following flow will likely be used to request an aggregated heatmap:

1.  Fetch all aggregated heatmap snapshots, which will return the minimum info needed to display a list of heatmaps.
2.  After the user picks a heatmap, fetch the full `AggregatedHeatmapSnapshot`, which will include the IDs for each device-specific `AggregatedPage`.
3.  Fetch a full `AggregatedPage` for a specific device using the ID found in the previous request.

## Get All Aggregated Heatmap Snapshots

> To get all aggregated heatmap snapshots for a project, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>/aggregated_page_snapshots"
-X GET
```

> The above request returns JSON structured like this:

```json
[
    {
        "id": 1,
        "title": "Snapshot 1",
        "query": "https://google.com",
        "created_at": "2019-12-31T02:52:03.298Z",
        "total_page_merges_count": 0,
        "state": "queued",
        "thumbnail": null
    },
    {
        "id": 2,
        "title": "Snapshot 2",
        "query": "https://instagram.com",
        "created_at": "2019-12-31T02:57:15.593Z",
        "total_page_merges_count": 0,
        "state": "queued",
        "thumbnail": null
    },
    {
        "id": 3,
        "title": "Test Name",
        "query": "https://google.com",
        "created_at": "2019-12-31T02:57:56.710Z",
        "total_page_merges_count": 0,
        "state": "queued",
        "thumbnail": null
    }
]
```
This endpoint fetches all aggregated heatmaps for a project.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>/aggregated_page_snapshots`

### URL Parameters

Parameter | Description
--------- | -----------
project_id | The identifier of the project

## Get an Individual Aggregated Page Snapshot

> To get an individual aggregated page snapshot, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/aggregated_page_snapshots/<snapshot_id>"
-X GET
```

> The above request returns JSON structured like this:

```json
{
    "id": 5,
    "title": "Title",
    "query": "query",
    "created_at": "2020-01-05T01:39:52.584Z",
    "total_page_merges_count": 44,
    "state": "completed",
    "thumbnail": null,
    "public": false,
    "token": "51q7NhZj4l46qZ3jNDRTTw",
    "avg_visit_duration": 19636.3636363636,
    "avg_page_order": 0,
    "total_looks_count": 0,
    "total_taps_count": 0,
    "phone": {
        "id": 19,
        "uid": "cool",
        "page_merges_count": 31,
        "device_type": "phone",
        "screen_width": 375.0,
        "screen_height": 812.0,
        "thumbnail": null
    },
    "tablet": null,
    "desktop": null
}
```

This endpoint retrieves an individual aggregated page snapshot, including barebones information on each device-specific `AggregatedPage`. A second request can be sent to request the full `AggregatedPage` for a specific device type.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/aggregated_page_snapshots/<snapshot_id>`

### URL Parameters

Parameter | Description
--------- | -----------
snapshot_id | The id of the aggregated page snapshot.

## Update an Individual Aggregated Page Snapshot

> To update an individual aggregated page snapshot, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/aggregated_page_snapshots/<snapshot_id>"
-X PUT
-d title="New Title"
-d public=false
```

> The above request returns JSON structured like this:

```json
{
    "id": 5,
    "title": "New Title",
    "query": "query",
    "created_at": "2020-01-05T01:39:52.584Z",
    "total_page_merges_count": 44,
    "state": "completed",
    "thumbnail": null,
    "public": false,
    "token": "51q7NhZj4l46qZ3jNDRTTw",
    "avg_visit_duration": 19636.3636363636,
    "avg_page_order": 0,
    "total_looks_count": 0,
    "total_taps_count": 0,
    "phone": {
        "id": 19,
        "uid": "cool",
        "page_merges_count": 31,
        "device_type": "phone",
        "screen_width": 375.0,
        "screen_height": 812.0,
        "thumbnail": null
    },
    "tablet": null,
    "desktop": null
}
```

This endpoint updates an aggregated page snapshot.

### HTTP Request

`PUT https://hawkeye-staging.herokuapp.com/api/v1/aggregated_page_snapshots/<snapshot_id>`

### URL Parameters

Parameter | Description
--------- | -----------
snapshot_id | The id of the aggregated page snapshot.
title | The updated title of the aggregated page snapshot.
public | The updated state of the aggregated page snapshot.

## Get an Individual Aggregated Page

> To get an individual aggregated page, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/aggregated_pages/<page_id>"
-d type=looks
-X GET
```

> The above request returns JSON structured like this:

```json
{
    "id": 1,
    "uid": "https://google.com",
    "page_merges_count": 0,
    "device_type": "phone",
    "screen_width": 200.0,
    "screen_height": 500.0,
    "thumbnail": null,
    "page_element_map": {
        "uid": "https://google.com",
        "screen_capture": null,
        "screen_width": 200.0,
        "screen_height": 300.0,
        "scroll_width": 200.0,
        "scroll_height": 400.0,
        "eye_positions_count": null,
        "taps_count": null,
        "average_looks": [],
        "root": null,
        "success_percentage": 1.0
    }
}
```

This endpoint retrieves an individual device-specific aggregated page. This includes the full page element map that can be used to display a heatmap. An optional `type` parameter can be used to request look, tap, or scroll heatmap data.

This request should be used to request a full heatmap for a specific device type after an `AggregatedPageSnapshot` has been retrieved.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/aggregated_pages/<page_id>`

### URL Parameters

Parameter | Description
--------- | -----------
page_id | The id of the aggregated page.
type | The type of heatmap to be returned. Includes look, tap, and scroll.

# Project Overview
The project overview endpoint returns a summary of activity for a project over the last week. This includes sessions that were created, heatmaps that were updated, testers who joined, and team activity, like notes. This makes it easy to provide an overview of a project without making numerous requests.

## Get Overview for a Project

> To fetch the overview for a project, send this request:

```shell
curl "https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>/overviews"
-X GET
```

> The above request returns JSON structured like this:

```json
{
    "sessions": [
        {
            "id": 6,
            "token": "bLHYPFAjjcv6-VWSbEYNDQ",
            "title": "Example Session",
            "content_type": "app",
            "started_at": "2019-06-13T10:12:12.000Z",
            "ended_at": "2019-06-13T10:20:12.000Z",
            "favorite": false,
            "created_at": "2020-06-16T22:53:45.679Z",
            "updated_at": "2020-06-16T22:53:45.679Z"
        }
    ],
    "heatmaps": [],
    "testers": [
        {
            "id": 7,
            "first_name": "Tester",
            "last_name": "Smith",
            "email": "tester@usehawkeye.com",
            "phone": null,
            "sessions_count": 1,
            "first_test_at": "2020-06-16T22:53:45.640Z",
            "last_test_at": "2020-06-16T22:53:45.640Z",
            "creator": {
                "id": 17,
                "first_name": null,
                "last_name": null
            },
            "avatar": null
        }
    ],
    "notifications": [
        {
            "id": 2,
            "created_at": "2020-06-17T05:59:27.599Z",
            "user": {
                "id": 17,
                "first_name": null,
                "last_name": null,
                "avatar": null
            },
            "notifiable_type": "Event",
            "count": null,
            "notification_type": null,
            "notifiable": {
                "id": 708,
                "started_at": null,
                "ended_at": null,
                "eventable_type": "Note",
                "eventable": {
                    "id": 20,
                    "content": null,
                    "resolved": false,
                    "upvotes": 0,
                    "user": {
                        "id": 17,
                        "first_name": null,
                        "last_name": null,
                        "avatar": null
                    },
                    "replies": []
                }
            }
        },
        {
            "id": 3,
            "created_at": "2020-06-17T06:03:49.196Z",
            "user": {
                "id": 17,
                "first_name": null,
                "last_name": null,
                "avatar": null
            },
            "notifiable_type": "Event",
            "count": null,
            "notification_type": "note",
            "notifiable": {
                "id": 709,
                "started_at": null,
                "ended_at": null,
                "eventable_type": "Note",
                "eventable": {
                    "id": 21,
                    "content": null,
                    "resolved": false,
                    "upvotes": 0,
                    "user": {
                        "id": 17,
                        "first_name": null,
                        "last_name": null,
                        "avatar": null
                    },
                    "replies": []
                }
            }
        }
    ],
    "daily_totals": {
        "2020-06-16 00:00:00 UTC": 1,
        "2020-04-02 00:00:00 UTC": 3,
        "2020-02-03 00:00:00 UTC": 1,
        "2019-10-13 00:00:00 UTC": 1
    },
    "weekly_totals": {
        "2020-06-15 00:00:00 UTC": 1,
        "2020-03-30 00:00:00 UTC": 3,
        "2020-02-03 00:00:00 UTC": 1,
        "2019-10-07 00:00:00 UTC": 1
    }
}
```

This endpoint fetches the overview for a specified project. Sessions, heatmaps, and testers follow the same JSON format as their dedicated endpoints.

TeamNotifications are used to track the actions team members perform on a project. For instance, a TeamNotification is created whenever a user makes a note on a session or invites subjects to participate in a test. A TeamNotification's `notification_type` can either be `note` or `session_invites`, with more types to be added. TeamNotifications can also have a polymorphic `notifiable` object attached. For notes, this is the `Event` object than attaches a `Note` to a `Session`. The `notifiable_type` describes what type of object is attached to the TeamNotification. Lastly, TeamNotifications have a `count` property that is used when the user performs multiple actions at once, like inviting several testers to the project.

### HTTP Request

`GET https://hawkeye-staging.herokuapp.com/api/v1/projects/<project_id>/overviews`

### URL Parameters

Parameter | Description
--------- | -----------
project_id | The id of the project.
