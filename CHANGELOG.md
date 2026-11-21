# Changelog

Every release of apogee-fortify, newest first. Each is a `vX.Y.Z` tag with a GitHub
release carrying its section; the `grav` line in `grav.toml` says which
compiler versions it builds with.

## Unreleased

- Registration, login with a two-factor challenge, logout, password reset by
  email, email verification by signed link, password confirmation and change,
  and two-factor setup with recovery codes (RFC 6238), as routes.
- `ModelUsers` for an Orbit model; `FortifyUsers` for anything else.
- Memory and database reset-token stores.
