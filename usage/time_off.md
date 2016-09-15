Time Off API Usage
==================
Our current Time Off API is an internal, beta version, not ready for public
usage.

Time Off Requests
-----------------
The most used feature of the Time Off API is requesting time off.

Steps to request time off:

1. Discover Time Off Types and Holidays

Request:

```http
GET /api/v1/time_off/requests/new?profile_id=f7e60ff5-7451-49a7-8807-455331bcd1ef HTTP/1.1
Authorization: Bearer <access token>
Accept: application/json
Host: company.namely.com
Connection: close
```

Response contains all the necessary information to build the UI for time off 
request: profile data, time off types, holidays.

```json
{
  "time_off_requests": [
    {
      "profile_id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
      "requester_id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
      "is_requester": true
    }
  ],
  "meta": {
    "count": 1,
    "status": 200,
    "current_user_date_format": "%m/%d/%Y",
    "has_profile": true
  },
  "links": {
    "time_off_requests.profile": {
      "type": "profiles"
    },
    "time_off_requests.requester": {
      "type": "profiles"
    },
    "time_off_requests.responder": {
      "type": "profiles"
    },
    "time_off_requests.time_off_types": {
      "type": "time_off_types"
    },
    "time_off_requests.holidays": {
      "type": "holidays"
    }
  },
  "linked": {
    "profiles": [
      {
        "id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
        "email": "test@example.com",
        ...
      }
    ],
    "time_off_types": [
      {
        "id": "8f02243e-fbac-4695-a37c-4e2ed3f0ff23",
        "title": "Vacation",
        "notes": "",
        "include_on_paystub": false,
        "calendar_options_alt_title": false,
        "calendar_options_strip_pattern": false
      }
    ],
    "holidays": [
      {
        "id": "4d344a59-710c-4973-aa87-28d3316d289d",
        "name": "a",
        "date": "07/02/2014",
        "year": 2014
      },
      ...
    ]
  }
}
```


2. Build time off request

After the user selects the time off type and the start and end dates of the
leave, the API can help the client side application build the correct objects
for requesting time off. Use the build API to find the list of days when the
  user should request days off.

```http
POST /api/v1/time_off/requests/build?profile_id=f7e60ff5-7451-49a7-8807-455331bcd1ef HTTP/1.1
Authorization: Bearer <access token>
Accept: application/json
Content-Type: application/json
Host: company.namely.com
Content-Length: 327

{
  "time_off_requests": [
    {
      "profile_id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
      "requester_id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
      "is_requester": true,
      "time_off_type_id": "8f02243e-fbac-4695-a37c-4e2ed3f0ff23",
      "leave_start": "2016-10-01",
      "leave_end": "2016-10-10"
    }
  ]
}
```

The response will include the time off days list along with validated fields:


```json
{
  "time_off_requests": [
    {
      "id": null,
      "duration": 0,
      "used_carryover": 0,
      "is_hourly": false,
      "is_requester": true,
      "hourly_multiplier": "8.0",
      "employee_notes": null,
      "manager_notes": null,
      "leave_start": "10/01/2016",
      "leave_end": "10/10/2016",
      "requires_approval": true,
      "approved": null,
      "time_off_type": "Vacation",
      "profile_id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
      "requester_id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
      "responder_id": null,
      "time_off_type_id": "8f02243e-fbac-4695-a37c-4e2ed3f0ff23",
      "time_off_plan_id": "e281e681-40c7-4615-bd4c-96b78f476698",
      "urls": [],
      "status": "Pending",
      "status_color": "yellow",
      "units": "days",
      "links": {
        "profile": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
        "requester": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
        "responder": null
      }
    }
  ],
  "meta": {
    "count": 1,
    "status": 200,
    "current_user_date_format": "%m/%d/%Y",
    "has_profile": true
  },
  "links": {
    "time_off_requests.profile": {
      "type": "profiles"
    },
    "time_off_requests.requester": {
      "type": "profiles"
    },
    "time_off_requests.responder": {
      "type": "profiles"
    },
    "time_off_requests.time_off_request_days": {
      "type": "time_off_request_days"
    }
  },
  "linked": {
    "profiles": [
      {
        "id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
        ...
      }
    ],
    "time_off_request_days": [
      {
        "id": null,
        "date": "10/01/2016",
        "used_accrual": 0,
        "used_carryover": "0.0"
      },
      {
        "id": null,
        "date": "10/02/2016",
        "used_accrual": 0,
        "used_carryover": "0.0"
      },
      {
        "id": null,
        "date": "10/03/2016",
        "used_accrual": 1,
        "used_carryover": "0.0"
      },
      {
        "id": null,
        "date": "10/04/2016",
        "used_accrual": 1,
        "used_carryover": "0.0"
      },
      {
        "id": null,
        "date": "10/05/2016",
        "used_accrual": 1,
        "used_carryover": "0.0"
      },
      {
        "id": null,
        "date": "10/06/2016",
        "used_accrual": 1,
        "used_carryover": "0.0"
      },
      {
        "id": null,
        "date": "10/07/2016",
        "used_accrual": 1,
        "used_carryover": "0.0"
      },
      {
        "id": null,
        "date": "10/08/2016",
        "used_accrual": 0,
        "used_carryover": "0.0"
      },
      {
        "id": null,
        "date": "10/09/2016",
        "used_accrual": 0,
        "used_carryover": "0.0"
      },
      {
        "id": null,
        "date": "10/10/2016",
        "used_accrual": 1,
        "used_carryover": "0.0"
      }
    ]
  }
}
```

3. Create the time off request by posting the request.

Notes:
 - Only 2 top level objects are required to create a request:
   `time_off_requests` and `time_off_request_days`. You can omit all the other
   ones (linked, links, meta).
 - `time_off_request_days` list becomes a top level object in the request.
 - You should omit all the fields expect `date` and `used_accrual` in the
   `time_off_request_days` objects.


```http
POST /api/v1/time_off/requests?profile_id=f7e60ff5-7451-49a7-8807-455331bcd1ef HTTP/1.1
Authorization: Bearer <access token>
Accept: application/json
Content-Type: application/json
Host: company.namely.com
Connection: close

{
  "time_off_requests": [
    {
      "id": null,
      "duration": 0,
      "used_carryover": 0,
      "is_hourly": false,
      "is_requester": true,
      "hourly_multiplier": "8.0",
      "employee_notes": null,
      "manager_notes": null,
      "leave_start": "10/01/2016",
      "leave_end": "10/10/2016",
      "requires_approval": true,
      "approved": null,
      "time_off_type": "Vacation",
      "profile_id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
      "requester_id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
      "responder_id": null,
      "time_off_type_id": "8f02243e-fbac-4695-a37c-4e2ed3f0ff23",
      "time_off_plan_id": "e281e681-40c7-4615-bd4c-96b78f476698",
      "urls": [],
      "status": "Pending",
      "status_color": "yellow",
      "units": "days",
      "links": {
        "profile": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
        "requester": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
        "responder": null
      }
    }
  ],
  "time_off_request_days": [
    {
      "date": "10/01/2016",
      "used_accrual": 0
    },
    {
      "date": "10/02/2016",
      "used_accrual": 0
    },
    {
      "date": "10/03/2016",
      "used_accrual": 1
    },
    {
      "date": "10/04/2016",
      "used_accrual": 1
    }
  ]
}
```

In the response the user receives the calculated time off request object:

```json
{
  "time_off_requests": [
    {
      "id": "617ccd47-4629-461f-80e9-fcb99f478fba",
      "duration": 1,
      "used_carryover": 0,
      "is_hourly": false,
      "is_requester": true,
      "hourly_multiplier": "8.0",
      "employee_notes": null,
      "manager_notes": null,
      "leave_start": "10/01/2016",
      "leave_end": "10/10/2016",
      "requires_approval": true,
      "approved": null,
      "time_off_type": "Vacation",
      "profile_id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
      "requester_id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
      "responder_id": null,
      "time_off_type_id": "8f02243e-fbac-4695-a37c-4e2ed3f0ff23",
      "time_off_plan_id": "e281e681-40c7-4615-bd4c-96b78f476698",
      "urls": [],
      "status": "Pending",
      "status_color": "yellow",
      "units": "days",
      "links": {
        "profile": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
        "requester": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
        "responder": null
      }
    }
  ],
  "meta": {
    "count": 1,
    "status": 200,
    "current_user_date_format": "%m/%d/%Y",
    "has_profile": true
  },
  "links": {
    "time_off_requests.profile": {
      "type": "profiles"
    },
    "time_off_requests.requester": {
      "type": "profiles"
    },
    "time_off_requests.responder": {
      "type": "profiles"
    }
  },
  "linked": {
    "profiles": [
      {
        "id": "f7e60ff5-7451-49a7-8807-455331bcd1ef",
        ...
      }
    ]
  }
}
```
