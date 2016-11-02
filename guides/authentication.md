Namely Authentication Methods
=============================
Namely currently supports 3 authentication methods:

- Email and Password
- Google OAuth 2
- SAML2

Companies can choose to authenticate their users with the following combinations:

- Email and Password + Google
- Google only
- SAML2 only

Use the company info endpoint to get the available authentication methods:

```
GET /api/v1/companies/info
```

Response:

```json
{
  "name": "Heat Agency",
  "permalink": "sales",
  "background_url": "/media/W1siZiIsIjIwMTQvMTAvMzEvMTcvMDIvMDQvMjA2L2trLmpwZyJdXQ/kk.jpg?sha=45aa5e61f12505cf",
  "logo_url": "/media/W1siZiIsIjIwMTYvMDQvMTYvMTkvNDkvMTYvMDg5ZGUwYTQtZDQyZS00MDkwLWFmMTctY2VmMzE1M2Q0NTI1L0hlYXQuanBnIl1d/Heat.jpg?sha=d43134901e803899",
  "authentications": [
    {
      "type": "email_password"
    },
    {
      "type": "google_oauth2",
      "init_url": "https://auth.namely.com/auth/google_oauth2?permalink=sales"
    }
  ]
}
```

Notes:
* If you are using Namely OAuth authentication, ignore the `init_url` field.
* `authentication` list has the list of available methods.



OAuth authentication
--------------------
When implementing OAuth 2 authentication with Namely, we recommend always sending the user to the `api/v1/oauth2/authorize` endpoint. Documentation on this: https://developers.namely.com/1.0/getting-started/authentication


