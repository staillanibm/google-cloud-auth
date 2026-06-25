# Google Cloud Auth

A small local web tool to run the Google OAuth2 consent flow for a GCP OAuth client and retrieve an access token and refresh token.

## Why

Useful when you need to manually obtain a refresh token for a GCP OAuth2 client (e.g. for testing API access to BigQuery, Cloud Storage, Pub/Sub, etc.) without writing a one-off script each time.

## Requirements

- Node.js 18+
- A GCP project with an OAuth 2.0 Client ID (type "Web application")
- An OAuth2 client created at [console.cloud.google.com/apis/credentials](https://console.cloud.google.com/apis/credentials)
- `http://localhost:3000/oauth2callback` added to the **Authorized redirect URIs** of that OAuth client

## Setup

```bash
npm install
```

## Usage

```bash
node server.js
```

Then open [http://localhost:3000](http://localhost:3000) in your browser:

1. Enter the **Client ID** and **Client Secret** of your GCP OAuth client.
2. Select the scopes you want to request (BigQuery, Cloud Storage, Pub/Sub, Drive, etc.).
3. Submit the form — you'll be redirected to Google's consent screen.
4. After granting consent, you'll be redirected back and the page will display the **access token** and **refresh token**.

The redirect URI is derived automatically from the request (`http://<host>/oauth2callback`), so no `.env` configuration is required for it.

## Notes

- The redirect URI registered in the GCP OAuth client **must** match exactly what this server uses (`http://localhost:3000/oauth2callback` by default).
- A refresh token is only returned if `access_type=offline` and `prompt=consent` are used (this is the default behavior here).
- If your OAuth client is in "Testing" publishing status, refresh tokens expire after 7 days. Switch the app to "Production" (after Google verification, if required by your scopes) for long-lived refresh tokens.
- Client ID, Client Secret, and authorization state are kept in memory only, for the duration of the OAuth round-trip — nothing is persisted to disk.
- Treat the access token and refresh token shown after consent as secrets: anyone holding the refresh token can mint new access tokens for the granted scopes until it is revoked or expires.

## Revoking a token

```bash
curl -X POST https://oauth2.googleapis.com/revoke \
  -d token=<access_token_or_refresh_token>
```
