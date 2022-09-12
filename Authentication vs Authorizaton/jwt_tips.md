# Refresh Token Rotation and Reuse Detection in Node.js

- With jwt authentication **an access token and a refresh token are issued after a successful login**.

- An **access token lasts for a short amount of time** think minutes here not hours and **a refresh token lasts for a longer amount of time** possibly one or more days.

- The access token is sent back as json data and a **front-end app should store it in memory but not in local storage or a cookie**.

- The refresh token is **sent to the browser as an http only secure cookie, it's not accessible by javascript but it does need to have an expiration set**.

- Every time the api is accessed, the access token is sent with the request, **when the access token expires the refresh token is used to request a new access token**, the refresh token is verified with its own endpoint and compared to data in the database, it needs to expire or be removed during a manual logout.

> If the refresh token was compromised, malicious access would be granted until the refresh token expires, and this brings us back to refresh token rotation, with refresh token rotation every time a new access token is issued a new refresh token is also issued, this doesn't eliminate the risk but it does greatly reduce it.

## Combining refresh tokens with reuse detection

- A refresh token can only be used once.

- If reuse is detected, all refresh tokens are invalidated for the user, which will force a new login for authentication.
