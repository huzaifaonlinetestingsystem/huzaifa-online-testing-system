# Online MCQ Examination Portal v3 — mobile-friendly + PostgreSQL

## Included
- Email verification required before a student/teacher can log in; expiring 24-hour verification links and resend option. Requires working SMTP settings.
- Admin-only full JSON database backup download and transactional restore; restore replaces current portal data, so make a fresh backup first.
- Configurable official WhatsApp, contact email, Facebook, Instagram, YouTube and website links in Admin → Site Settings.
- Responsive mobile-first public website and admin dashboard.
- Student and teacher registration/login with bcrypt password hashing.
- PostgreSQL database for users, questions, exams, attempts and results.
- PostgreSQL-backed sessions (survive application restarts).
- Scheduled exams, server-controlled start window and server deadline.
- One attempt per user per exam, enforced by a database unique constraint.
- Automatic marking and result lookup.
- Printable certificate for candidates who meet the pass percentage.
- Admin login rate limited to 6 attempts per 15 minutes per IP.
- Admin can add questions, create/activate exams, view accounts and results.
- Mobile navigation, large touch targets, responsive forms and question cards.

## Local run
1. Install Node.js 18+ and PostgreSQL.
2. Create a database, e.g. `mcq_portal`.
3. Copy `.env.example` to `.env`; set `DATABASE_URL`, a unique random `SESSION_SECRET` (32+ characters), `ADMIN_USERNAME`, and a strong unique `ADMIN_PASSWORD`. Configure `APP_BASE_URL` and working SMTP credentials or registration email verification will remain unavailable. Never commit `.env` to source control.
4. Ensure `TEST_OPEN_AT` and `TEST_CLOSE_AT` include a timezone offset, e.g. `2026-11-01T10:00:00+05:00`.
5. Run `npm install` and `npm start`.
6. Open `http://localhost:3000`; admin is at `/admin.html`.
7. The database schema and a few demo MCQs are created automatically on first start.

## Hosting and database
For 100–500 participants, use a managed PostgreSQL database and a Node.js host. A free tier may be sufficient for a pilot but no free plan guarantees 100–500 simultaneous test-takers. Check current provider limits, sleeping/cold-start behavior, database expiry, connection limits and persistent storage before launch. Keep a database backup and load-test with expected concurrency.

The app expects a PostgreSQL URL in `DATABASE_URL`. Do not deploy with a local SQLite file or a database that can be erased on restarts. A managed database can be shared by the web app and the session store. The connection pool is capped at 15 per app instance; check your provider's database connection limit.

## Important limitations / production hardening
- Set `NODE_ENV=production` so session cookies require HTTPS. Deploy behind HTTPS.
- Admin credentials are configured as environment variables. Use a long unique password and rotate it if exposed. Login is rate limited; add stronger account lockout, admin MFA and audit logs for production.
- The first user who registers can select student or teacher. If roles need approval, add admin approval before launch.
- A started attempt is reserved in the database. If a student closes their browser, the one-attempt restriction still applies; an administrator must decide how to handle interrupted attempts.
- Timer expiry currently blocks late submissions but does not automatically finalize an expired attempt; add a scheduled finalizer or permit a carefully validated expiry submission before a formal exam.
- Certificate is printable and not digitally signed/verifiable. Configure your real contact details in Admin → Site Settings before launch. The owner’s real phone number, email and social account URLs were not provided, so those fields intentionally start blank.
- Backup restore replaces all current portal data and logs users out. Store backups securely because they contain personal data. For disaster recovery, also enable scheduled provider-side backups and test restore procedures.
- Email verification requires a reachable SMTP service and the exact deployed public URL in `APP_BASE_URL`; links expire after 24 hours. Do not use a personal SMTP password if your provider offers app passwords or API credentials.
- This is a starter project, not certified examination software. Test thoroughly before official use. Do not put real exam questions or student data into a public demo.


## Version 4 updates
- Public contact defaults set to WhatsApp `+92 300 7136714` (stored as `923007136714`) and `muhammadsahilfaizi@gmail.com`. Confirm these are correct before deployment. Social accounts remain blank until you provide official links.
- Manual account approval: users must verify their email and then an administrator must approve their account before they can log in and sit an exam. Approval controls are in the admin dashboard.
- Installable Progressive Web App (PWA): deploy on HTTPS, then open the website in Chrome on Android and use **Install app** / **Add to Home screen**. This is an installable web app, not a signed standalone APK.
- To become admin, configure unique `ADMIN_USERNAME` and `ADMIN_PASSWORD` environment variables in the hosting provider. Do not put real admin credentials in source files or share them publicly.
- Set `APP_BASE_URL`, SMTP credentials, PostgreSQL `DATABASE_URL`, and a random `SESSION_SECRET` in the host's environment settings.
- Backup and restore are available inside the admin dashboard. Restore overwrites existing portal data; download a fresh backup before restoring. Keep backups encrypted/private.


## Huzaifa branding and Google Play preparation
- App/site display name: **Huzaifa Online Testing System**.
- Default contact: WhatsApp `+92 300 7136714`; email `muhammadsahilfaizi@gmail.com`.
- The website is an installable PWA after deployment on HTTPS. A Google Play Android App Bundle is **not** included because a real HTTPS domain, Android package ID, signing setup and Play Console account are needed first.
- See `android-playstore/README_UR.md` for the Play Store wrapper/build process and free-tier caveats.
