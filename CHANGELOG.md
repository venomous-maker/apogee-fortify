# Changelog

Every release of apogee-fortify, newest first. Each is a `vX.Y.Z` tag with a GitHub
release carrying its section; the `grav` line in `grav.toml` says which
compiler versions it builds with.

## Unreleased

## 0.1.1 (2026-11-27)

### Fixes

- Database connections that the server dropped while idle (MySQL's `wait_timeout`, a restart, a proxy's idle limit) reconnect on their next use instead of failing with "server has gone away" (grav-db 0.2.1). Uses the new releases of apogee-crypt, apogee-mail, apogee-package, grav-db, grav-orbit.

## 0.1.0 (2026-11-25)

- Registration, login with a two-factor challenge, logout, password reset by
  email, email verification by signed link, password confirmation and change,
  and two-factor setup with recovery codes (RFC 6238), as routes.
- Two-factor codes and secrets come from std's `Totp` and `Base32` (needs grav
  1.13.0 or newer).
- `ModelUsers` for an Orbit model; `FortifyUsers` for anything else.
- Reset tokens as an Orbit model with its migration; an Orbit store and a
  memory one; a migration adding `email_verified_at` and `two_factor` to the
  users table when missing.
- `FortifyPackage`: connects itself to an apogee-framework app (settings, the
  routes in the web group, the users from auth.model, the links by email).

### Dependencies

- Uses apogee-http 0.3.0, apogee-routing 0.3.0, apogee-session 0.2.2, apogee-validation 0.2.1, apogee-auth 0.2.2, apogee-crypt 0.2.0, apogee-mail 0.3.0, apogee-package 0.1.0, grav-orbit 0.3.0, apogee-testing 0.1.1 (from their GitHub tags).
