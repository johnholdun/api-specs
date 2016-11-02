Notifications Usage
===================


Overview
--------
We have 3 types of notifications: "site", "email", and "push".
"Email" and "push" are both directly tied to "site" being enabled.  

Push notifications are sent via Amazon SNS to the relevant users' registered
devices.

The following events are currently supported:

  1.  [Creating a post](https://developers.namely.com/1.0/events/create-event-1)
      or comment with an @mention in the body.
      An @mention is formatted using markdown link syntax --

      * \[Profile Name](/profiles/<guid>)
      * \[Team Name](/teams/<guid>)
      * \[Group Name](/groups/<guid>)

      For example, if a post is created with `"Hi [David](/profiles/123)"` as
      the content, then this will trigger a push notification via SNS to the user
      with id "123". Please note that the target of the @mention must actually
      exist in the company in order to be treated as an @mention.
  2.  Time off created
  3.  Time off created on behalf
  4.  Time off destroy
  5.  Time off edit
  6.  Time off responded


JSON Format
-----------
All API requests must specify the `application/json` format.  
This can be done by:  

1.  Specifying it in the request header:
    ```http
    Accept: application/json
    Content-Type: application/json
    ```
2.  Or by appending .json to the end of the API endpoints:  
    `GET /api/v1/notifications.json`


User Devices API
----------------
A device (such as a phone) must be registered to a user in our system and in turn,
we register it with Amazon SNS to establish an endpoint. Once a device is registered,
we automatically send push notifications to it when one of the events specified above
occurs.

1.  Create a device associated to the current user. Registers device with SNS to establish
    an endpoint.  
    __POST /api/v1/profiles/me/devices__:
    ```http
    POST /api/v1/profiles/me/devices HTTP/1.1

    {"devices":{"token": "tokenstring", "platform": "ios", "name": "iPhone" }}
    ```

    Payload parameters:
    * `token` (string) -- unique string specific to a device
    * `platform` (string) -- can be "ios" or, in the near future, "android"
    * `name` (string) -- description of device
2.  Delete a device registered to a the current user.
    __DELETE /api/v1/profiles/me/devices/:token__:
    ```http
    DELETE /api/v1/profiles/me/devices/tokenstring HTTP/1.1
    ```


Notifications API
-----------------
1.  Retrieve current user's notifications. This currently returns all of a user's
    notifications, but eventually this will be limited to only 10.  
    __GET /api/v1/notifications__:
    ```http
    GET /api/v1/notifications HTTP/1.1
    ```
    __Response__:
    ```json
    {
      "notifications": [
        {
          "id": 1254,
          "text": "Notification text",
          "type": "",
          "timestamp": 1472068398,
          "seen": true,
          "read": false,
          "links": null
        },
        {
          "id": 1252,
          "text": "Other text",
          "type": "",
          "timestamp": 1471893944,
          "seen": true,
          "read": false,
          "links": null
        },
        {
          "id": 60,
          "text": "You are assigned to write a review for Person.",
          "type": "",
          "timestamp": 1459882026,
          "seen": true,
          "read": true,
          "links": null
        }
      ],
      "links": {},
      "linked": {
        "profiles": [],
        "job_titles": [],
        "files": []
      }
    }
    ```

    Query parameters:
    1.  `limit` (integer) -- number of notifications to retrieve at a time (will
        be a max of 10 going forward)
        ```http
        GET /api/v1/notifications?limit=10 HTTP/1.1
        ```
    2.  `skip` (integer) -- number of notifications to skip over
        ```http
        GET /api/v1/notifications?skip=10 HTTP/1.1
        ```
2.  Mark all of the current user's notification as seen and sends a badge update
    via SNS with the number of unseen notifications (0 in this case).  
    __PATCH /api/v1/notifications/mark_all_seen__:
    ```http
    PATCH /api/v1/notifications/mark_all_seen HTTP/1.1
    ```
3.  Marks all of the current user's notifications as read (includes marking as seen).
    Sends badge update via SNS with number of unseen notifications (0 in this case).  
    __PATCH /api/v1/notifications/mark_all_read__:
    ```http
    PATCH /api/v1/notifications/mark_all_read HTTP/1.1
    ```
4.  Update an individual notification's read/seen value.  
    __PATCH/PUT /api/v1/notifications/:id__:
    ```http
    PATCH /api/v1/notifications/123 HTTP/1.1

    {"notification": {"seen": true}}
    ```

    Payload parameters:
    * `seen` (boolean) -- marks a notification seen or unseen
    * `read` (boolean) -- marks a notification read or unread

    Marking a notification as seen, unseen, and read will trigger a badge update via SNS.  
    Marking a notification as unread will not trigger a badge update since the badge
    number reflects the number of unseen notifications, as opposed to unread.


User Push Notification Settings API
-----------------------------------

1.  Retrieve the current user's push notification settings.  
    __GET /api/v1/settings/account/push_notifications__:
    ```http
    GET /api/v1/settings/account/push_notifications HTTP/1.1
    ```
    
    __Response__:
    ```json
    {
      "settings": [
        {
          "id": "time_off_created",
          "name": "New time off request",
          "type": "boolean",
          "value": true,
          "readonly": false,
          "description": null
        },
        {
          "id": "time_off_destroy",
          "name": "Deleted time off request",
          "type": "boolean",
          "value": true,
          "readonly": false,
          "description": null
        },
        {
          "id": "time_off_responded",
          "name": "Response to time off request",
          "type": "boolean",
          "value": true,
          "readonly": false,
          "description": null
        },
        {
          "id": "time_off_edit",
          "name": "Edited time off request",
          "type": "boolean",
          "value": true,
          "readonly": false,
          "description": null
        },
        {
          "id": "mention_user",
          "name": "I've been mentioned",
          "type": "boolean",
          "value": true,
          "readonly": false,
          "description": null
        },
        {
          "id": "mention_team",
          "name": "A team I'm on has been mentioned",
          "type": "boolean",
          "value": true,
          "readonly": false,
          "description": null
        }
      ]
    }
    ```
2.  Update the current user's push notification settings.  
    __PATCH/PUT /api/v1/settings/account/push_notifications/:id__:
    ```http
    PATCH /api/v1/settings/account/push_notifications/mention_user HTTP/1.1

    {"settings": {"value": "false"}}
    ```

    Payload parameters:
    * `value` (string) -- "true" or "false" to enable or disable the setting

