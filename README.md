# Fursat

**Log off. Live more.**

Booking and admin app for Fursat, an offline adventure resort and digital detox retreat. Guests book short phone-free stays, seal their phones in a locker at check-in, and spend the days on treks, cooking, art, sport and bonfires. Companies book team retreats, and friends and family can gift an escape.

## Tech stack

- Next.js 14 (App Router) and React 18
- Tailwind CSS 3
- Prisma 5 with SQLite (free, no database server needed)
- NextAuth for login
- Razorpay for payments (test mode is free)
- Nodemailer for confirmation emails

## What you need first

- Node.js 18.17 or newer (check with `node -v`)
- VS Code

## Setup

1. Open a terminal in the project folder (the one that contains this README).

2. Install the packages:
   ```
   npm install
   ```

3. Open `.env` and fill in the values. For a first run you only need to change `NEXTAUTH_SECRET`. Generate a secret with:
   ```
   node -e "console.log(require('crypto').randomBytes(32).toString('hex'))"
   ```
   Payments and email will not work until you add Razorpay test keys and SMTP details, but the rest of the app runs without them.

4. Create the database (this needs `prisma/schema.prisma`, which is built in a later step):
   ```
   npm run db:push
   npm run db:seed
   ```

5. Start the app:
   ```
   npm run dev
   ```

6. Open http://localhost:3000 in your browser. The admin dashboard is at http://localhost:3000/admin/dashboard.

## Scripts

| Command | What it does |
| --- | --- |
| `npm run dev` | Starts the app in development mode |
| `npm run build` | Builds the app for production |
| `npm run start` | Runs the production build |
| `npm run lint` | Checks the code for problems |
| `npm run db:push` | Creates or updates the database tables from the schema |
| `npm run db:seed` | Adds starter data and the first admin account |
| `npm run db:studio` | Opens a visual database browser |

## Folder layout

```
fursat/
  app/                  Pages and API routes
    admin/              Admin pages (dashboard, bookings, lockers, ...)
    api/                Backend routes (bookings, payments, check-in, ...)
    retreats/           Public retreat pages
    booking/            Booking flow
    gift/               Gift voucher page
    corporate/          Team retreat enquiry
    checkin/            Digital check-in and phone locker
  components/           Reusable interface pieces
  lib/                  Database, auth, payments, email, pricing helpers
  prisma/               Database schema and seed script
  public/               Images and logo
  styles/               Global styles
```

## Keep these safe

- Never share or upload `.env`. Make sure `.env` is listed in `.gitignore` before you use git.
- Use Razorpay **test** keys while building. Switch to live keys only when you launch.

## Troubleshooting

- **`node` is not recognized:** install Node.js LTS from nodejs.org, then fully close and reopen VS Code.
- **PowerShell says scripts are disabled:** switch the terminal to Command Prompt (the dropdown next to the + in the terminal panel), or run `Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned` once.
- **Page shows a 404:** check the folder and file names exactly, for example `app/admin/dashboard/page.js`.
- **Port 3000 is busy:** run `npm run dev -- -p 3001` and open http://localhost:3001.
