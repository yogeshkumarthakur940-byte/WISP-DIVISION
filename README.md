# ⚡ Wisp Division v4
Esports organisation website with **sign up / login**, an **admin dashboard that edits everything**, trial applications and a members-only payout area.
Zero npm dependencies · Node.js 18+ · data stored in `data/*.json`.

## Run
```
node server.js        # open http://localhost:3000
node test.js          # 32 automated tests
```
**Admin login:** click **Login** and enter the Admin ID and password stored in `admin.local.js` (the same box team members use; members type their email). Whoever logs in with that ID + password becomes admin and can edit everything.
`admin.local.js` is in `.gitignore` so it is **not uploaded to GitHub**. On a host you can use `ADMIN_ID` / `ADMIN_KEY` environment variables instead. If neither is set, the site asks you to create an admin on first run (setup code printed in the server console).
Set `COOKIE_SECURE=1` when using HTTPS.

## Roles
| Role | Can do |
|---|---|
| Guest | View site, apply for trial, check result by Application ID |
| Pending | Signed up, waiting for approval |
| Member | Everything above + see weekly payouts |
| Admin | Edit settings, games, roster, matches, FAQ, payouts; review applications; approve users |

## Admin dashboard (Login → Admin button)
Settings (incl. About, social links, **open/close trials**) · Games · Roster · Matches · **News** · **Achievements** · Applications · **Messages** inbox · Payments · FAQ · Users (approve members)

## Trials
Applicants choose a role (Rusher, Secondary Rusher, Nader, Sniper), say if they can be IGL (Yes/No) and give a mobile number (admin-only). Every applicant appears on the public **Results board** as 🟡 Pending, 🟢 Selected or 🔴 Not Selected. Admins must write a reason (min 5 characters) when declaring a result; it is shown publicly. Application IDs and mobile numbers are never shown publicly.

## Security built in
- Passwords hashed with scrypt, HttpOnly + SameSite=Strict session cookie, 8+ char minimum
- Rate limits on login, sign-up, applications and result lookups
- Every field is whitelisted, length-limited and enum-checked on the server (clients can't inject extra data)
- Applicants can't self-select (status is forced to Pending); IDs can't be overwritten
- Discord webhook is stored privately and fired by the server (never sent to visitors)
- CSRF header check, CSP and security headers; last admin can't be demoted
- Password hashes never leave the server; admin credentials live in a git-ignored file / env vars, and logins are rate-limited
- First-run setup is protected by a one-time code shown only in the server console

## Structure
```
server.js     API, roles, validation      lib/db.js    JSON file database
lib/auth.js   hashing + sessions          lib/seed.js  default content (first run)
public/index.html   the site + admin      test.js      automated tests
```
Deploying: any Node host (Render, Railway, VPS). Keep the `data/` folder on a persistent disk and back it up.
