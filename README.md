# apogee-fortify

The sign-in flows every [Apogee](https://github.com/venomous-maker) app needs,
as routes with no views (the app draws its own pages; JSON clients get JSON):
registration, login with an optional two-factor challenge, logout, password
reset by email, email verification by signed link, changing the password,
confirming it before sensitive actions, and two-factor setup with recovery codes.

In an apogee-framework app, importing the module is all it takes:
`FortifyPackage` mounts the routes in the `web` group, takes the users from
`auth.model` (an Orbit model with `email`, `password`, `emailVerifiedAt` and
`twoFactor`), sends the reset and verification links by email through the
app's mailer, and adds two migrations (run them with `migrate`):
`password_reset_tokens`, and the `email_verified_at` and `two_factor` columns on
the users table (added only when missing).

```typescript
import "std"
import "apogee-fortify"
use apogee.*

router.get("/billing", billing).middleware(["auth", "verified", "password.confirm"])
Fortify.sendResetLink = fn(user: any, url: string) { ... }   // only to send the links some other way
```

Outside the framework, set `Fortify.users` (`new ModelUsers(reflect(User))`),
`Fortify.auth`, `Fortify.sendResetLink` and `Fortify.sendVerificationLink`,
then call `Fortify.routes(router)` inside a group with the session.

## Routes

| Route | Does |
|---|---|
| `POST /register` | name, email, password (+ confirmation): creates the user, signs them in, sends the verification link |
| `POST /login` | email, password, remember: signs in, or answers `{"two_factor": true}` when a code is needed |
| `POST /two-factor-challenge` | `code` (from the app) or `recovery_code`: finishes the sign-in |
| `POST /logout` | signs out and starts a fresh session |
| `POST /forgot-password` | emails a reset link (the same answer whether or not the address has an account) |
| `POST /reset-password` | token, email, password (+ confirmation) |
| `GET /email/verify/{id}/{hash}` | the signed link from the email (an hour by default) |
| `POST /email/verification-notification` | sends the link again |
| `POST /user/confirm-password` | for `password.confirm` (three hours by default) |
| `PUT /user/password` | current_password, password (+ confirmation) |
| `POST /user/two-factor-authentication` | a new secret, its `otpauth://` address (for a QR code) and 8 recovery codes, shown once |
| `POST /user/confirmed-two-factor-authentication` | a code from the app turns two-factor sign-in on |
| `DELETE /user/two-factor-authentication` | turns it off |
| `POST /user/two-factor-recovery-codes` | new recovery codes (the old ones stop working) |

A browser's form post gets a redirect (`Fortify.home` after signing in) with
a `status` flash; validation errors come back the usual way (422 JSON, or back
with errors). Middleware: `verified` (email verified) and `password.confirm`.

## Security

- Passwords are hashed with apogee-auth's `Passwords`; old hashes are upgraded at sign-in.
- Sign-in, two-factor and resend attempts are rate-limited (`loginsPerMinute`).
- Reset tokens are random, kept only as SHA-256 hashes, expire after an hour,
  are used once, and are not reissued within a minute.
- Two-factor codes are RFC 6238 (any authenticator app), made and checked by
  std's `Totp` (grav 1.13); a code is good once,
  the secret is stored encrypted (apogee-crypt) and recovery codes only as hashes.
- The session is regenerated at sign-in and invalidated at logout.

## Settings

| Setting | Default |
|---|---|
| `fortify.home` | `FORTIFY_HOME`, `/home` |
| `fortify.registration`, `reset_passwords`, `email_verification`, `update_passwords`, `two_factor` | on |
| `fortify.users_table` | `users` (for the columns migration) |
| `fortify.issuer` | `APP_NAME` (shown in authenticator apps) |
| `fortify.logins_per_minute` | 5 |
| `fortify.reset_url` | `/reset-password/{token}?email={email}` (after `app.url`) |

Reset tokens are an Orbit model (`PasswordResetToken`) kept by
`OrbitResetTokenStore`; `MemoryResetTokenStore` is for tests. `passwordRules`,
`passwordTimeout` and `verificationLinkSeconds` can be changed on `Fortify`.
`ModelUsers`' field names can be changed, or `FortifyUsers` implemented for any
other storage.

## Tests

```sh
grav test tests/fortify.grav
grav test tests/package.grav
```

## License

MIT
