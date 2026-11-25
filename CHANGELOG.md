# Changelog

Every release of apogee-fortify, newest first. Each is a `vX.Y.Z` tag with a GitHub
release carrying its section; the `grav` line in `grav.toml` says which
compiler versions it builds with.

## Unreleased

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
