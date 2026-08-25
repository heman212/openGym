# Security 
  still locks out one user completely.
- **CSRF protection is `SameSite=Lax` and nothing else.** There are no CSRF tokens.
- **User verification is preferred, not required.** Both handshakes pass
  `requireUserVerification: false` (`api/server.js:297`, `api/server.js:343`), so a passkey
  released without a biometric or PIN is still accepted. In practice: unlocked device ≈ account
  access.
- **One passkey per profile, and no recovery.** Every successful registration creates a *new*
  profile (`api/server.js:309-319`); there is no route to attach a second passkey to an existing
  one, and no email or reset path. Lose the passkey and that profile is unreachable — only direct
  surgery on `./data` gets it back.
- **Disabling someone isn't a ban.** They can still register a fresh profile with a new passkey
  unless `INVITE_ONLY=1` is set.
- **HTTPS is required and the app doesn't provide it.** The API container speaks plain HTTP and
  nginx listens on `:80` (`web/nginx.conf`); TLS is your reverse proxy's job. Without it,
  browsers won't do passkeys at all (except on `http://localhost`) and the session cookie is sent
  in the clear.
- **No rate limiting anywhere.** Nothing throttles logins, registrations or writes, and
  `POST /api/register/options` still answers whether an invite code is valid
  (`api/server.js:272`), so an invite-only instance on the open internet should have a rate limit the browser's
  `localStorage` and is gone when the browser storage is cleared.o
