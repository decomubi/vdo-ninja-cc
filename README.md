Using Your Own Database (Optional)
If you want to connect your own database instead of using the default one, follow these steps.
If you’re happy using the existing database, you can skip this section.

Requirements
GitHub account
Netlify account
Neon (PostgreSQL) account


1. Fork the repository
Open the GitHub repository link you received.
Click the Fork button (top‑right).
Select your GitHub account and create the fork.
You now have your own copy of the code, fully under your control.

2. Create your own Neon database
Go to https://neon.tech and sign in.

Create a new project (or a new database in an existing project).

In the Neon UI, copy your connection string, for example:
postgresql://your_user:your_password@your-host.neon.tech/your_db?sslmode=require


In the SQL editor for this database, run:
CREATE TABLE IF NOT EXISTS csv_items (
  id             serial PRIMARY KEY,
  group_name     text      NOT NULL,
  seat_num       integer   NOT NULL,
  streamer_label text,
  director_label text,
  url_label      text,
  view_link      text,
  guest_link     text,
  director_link  text,
  created_at     timestamptz DEFAULT now()
);

This is the only table the app needs.

3. Deploy your own Netlify site
Go to Netlify → Add new site → Import an existing project.

Choose GitHub and select your fork of the repo.

On the “Configure your project” screen, use these settings:

Branch to deploy: main
Base directory: (leave empty)
Build command: npm run build
Publish directory: .
Functions directory: functions

Click Deploy site.

Netlify will build and publish your own copy of the app with serverless functions.

4. Link Netlify to your Neon database
Open your new site in Netlify.

Go to Site settings → Build & deploy → Environment → Environment variables.

Click Add a variable and enter:

Key: NEON_DATABASE_URL

Value: your Neon connection string, e.g.
postgresql://your_user:your_password@your-host.neon.tech/your_db?sslmode=require

Mark as Contains secret values (if available).

Scope: All scopes / Same value for all deploy contexts.

Save the variable.

Go to the Deploys tab and click
Trigger deploy → Clear cache and deploy site.

After this deploy, all functions on this site (upload-csv, get-csv) will use your database.

5. Verify it’s using your database
Open your site, e.g.:
https://your-site-name.netlify.app/#admin

In the Admin tab, upload a CSV file.

Under “Upload Data” you should see a message like:
Uploaded to server (X items).

In another browser tab, open:
https://your-site-name.netlify.app/.netlify/functions/get-csv
You should see JSON matching your uploaded CSV (same groups/seats).

You can also verify directly in Neon:
SELECT * FROM csv_items;

You should see the same rows.
