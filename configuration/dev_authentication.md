---
description: GoCD Authentication
keywords: GoCD configuration, authentication, https, ldap, ldap authentication, authorization plugins, user access, passwords, logins, authorization endpoints, GoCD security
---

# Authentication

GoCD was built from the bottom up with security in mind. GoCD server provides both an http service and an https service by default. The http service listens on port 8153 and the https service listens on port 8154.

Support for in-built Password-File based and LDAP authentication has been disabled. Alternatively, GoCD provides extensions through [Authorization Endpoints](https://plugin-api.gocd.org/current/authorization/) using which plugins can provide authentication and authorization services backed by any authorization service providers like LDAP, Google, Github etc.

By default, GoCD does not require users to authenticate. However, users can be forced to authenticate by installing and configuring any of the [Authorization plugins](https://plugin-api.gocd.org/current/authorization/). GoCD comes bundled with a [Password File based](https://github.com/gocd/filebased-authentication-plugin) and [LDAP/AD](https://github.com/gocd/gocd-ldap-authentication-plugin) authentication plugins.

You should be able to configure any number of Authorization plugins, GoCD would attempt authenticating the user with each plugin (in no specific order) until a successful authentication.

GoCD forces a perodic re-authentication of users, this is to ensure any changes like removing of users or roles in the external authorization server are reflected in GoCD. The re-authentication interval is controlled by the system property `go.security.reauthentication.interval` which is defaulted to 30 minutes. The plugins could potentially be written in a way to detect such changes in the external system, and [notify](https://plugin-api.gocd.org/current/authorization/#invalidate-users-cache) GoCD server to force a reauthentication of users.

##### Session configuration

You could use one or more of the below to tweak the session config:

| System property name | Default value | Description |
|-----------|----------|-------------|
| `go.server.session.timeout.seconds` | `1209600` (14 days)  | Idle session timeout (in seconds). Do not set this to `-1` else the session will never timeout. |
| `go.sessioncookie.maxage.seconds` | `1209600` (14 days)  | Expiry of a session cookie. Set to `-1` to expire the cookie on browser close. Do not set to `0` as this will cause the cookie to expire immediately.|
| `go.sessioncookie.secure` | `N`  | Set this to `Y` to allow a session cookie to be set only if the request was made over HTTPS. Do not set this property if the users access GoCD via HTTP url|

## Controlling User Access

GoCD checks if an authenticated user is an existing user or a new user (logging in for the first time). For new users, there are two behaviors GoCD can operate under:

-   Automatically register the new user in GoCD and continue with the login process.
-   Deny access to the user if not already a registered GoCD user. New users will have to be explicitly added by an admin.

To switch the mode in which the GoCD Server operates:

1.  Login to GoCD as an admin
2.  Navigate to the "Admin" section
3.  Click on the "Server Configuration" tab
4.  Set the "Allow users to login via plugin into GoCD, even if they haven't been explicitly added to GoCD." checkbox

![](../resources/images/user_authentication_auto_login.png)
