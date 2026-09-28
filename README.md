# In4Tech company website

A beginner-friendly company website with an HTML/CSS/vanilla JavaScript frontend, an Express REST API, and a MySQL database. The contact form saves real submissions to MySQL. The admin page lets an authorized user sign in, review, open and delete those submissions.

The visual identity, copy, illustrations and sample case studies in this project are original placeholders. Replace the sample company information and legal pages before publishing.

## Project structure

```text
in4tech/
├── frontend/                 Website pages, styles, scripts and assets
│   ├── index.html             Main company website
│   ├── admin.html             Private admin sign-in and inbox
│   ├── privacy.html           Privacy policy starter text
│   ├── terms.html             Terms of use starter text
│   ├── css/style.css
│   ├── js/script.js
│   ├── js/admin.js
│   └── assets/
├── backend/                  Express app, routes and database access
├── database/schema.sql       MySQL database and contacts table
├── .env.example              Environment variable template
├── package.json
└── README.md
```

## 1. Required software

- Node.js 18 or newer and npm
- MySQL Server 8 or compatible MySQL server
- A browser

Check Node and npm in PowerShell:

```powershell
node --version
npm.cmd --version
```

In this Windows workspace, PowerShell blocks the `npm` PowerShell script, so use `npm.cmd` for npm commands. MySQL must be installed and running before the contact API can store data. If the `mysql` command is not recognized after installation, use MySQL Workbench or add the MySQL `bin` directory to PATH.

## 2. Install Node.js and MySQL

Install the current Node.js LTS release from [nodejs.org](https://nodejs.org/). The installer includes npm.

Install MySQL Community Server from [dev.mysql.com/downloads/mysql](https://dev.mysql.com/downloads/mysql/) or use MySQL Workbench. During setup, set a root password and make sure the MySQL service is running.

## 3. Create the database

Open PowerShell in the project folder and import the schema. The MySQL installer may not add its command-line tools to PATH; if so, use its full path as shown:

```powershell
Get-Content -Raw .\database\schema.sql | & 'C:\Program Files\MySQL\MySQL Server 8.0\bin\mysql.exe' -u root -p
```

Enter the MySQL password when prompted. You can instead open `database/schema.sql` in MySQL Workbench and run it there. The script creates the `company_website` database and `contacts` table.

## 4. Configure environment variables

Copy the example file to `.env` in the project root:

```powershell
Copy-Item .env.example .env
```

Edit `.env` and set `DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASSWORD` and `DB_NAME` to match your local MySQL setup. Set a long random `SESSION_SECRET` as well. `.env` is ignored by Git and must never be committed.

Generate a bcrypt admin password hash. Choose a password with at least 12 characters:

```powershell
npm.cmd run admin:hash -- "your-long-admin-password"
```

Copy the printed hash into `ADMIN_PASSWORD_HASH` in `.env`. Set `ADMIN_USERNAME` to the username you want. The password itself is not stored in frontend code or in the database.

## 5. Install dependencies and run

```powershell
npm.cmd install
npm.cmd run dev
```

Open [http://localhost:5000](http://localhost:5000). The same Express server serves the frontend and API. For production-style start use `npm.cmd start`.

## 6. Verify setup

Visit [http://localhost:5000/api/health](http://localhost:5000/api/health). When MySQL is configured and available, it returns `{"status":"ok","database":"connected"}`. A `503` response with `degraded` means the web server is running but it cannot reach the configured database.

Submit the contact form on the home page. On success, it displays a confirmation without reloading. The row is inserted into `company_website.contacts` with a parameterized SQL statement. If MySQL is not installed/configured, the endpoint reports a generic server error and cannot save contacts.

## 7. Use the admin inbox

Open [http://localhost:5000/admin.html](http://localhost:5000/admin.html) and sign in with `ADMIN_USERNAME` and the original password used to generate `ADMIN_PASSWORD_HASH`. The authenticated dashboard lists the most recent 250 submissions. Choose **View** to see a message or **Delete** to remove it. Sign out to end the session.

This starter uses Express's in-memory session store. It is suitable for local development, but production should use a persistent session store and HTTPS. The admin API is protected by the session middleware; keep `.env` private.

## 8. Change website content

- Edit headings, paragraphs and page sections in `frontend/index.html`.
- Change colors, spacing, responsive layouts and visual styles in `frontend/css/style.css` (the design tokens are at the top).
- Change navigation, animations, counters, filters, testimonial slides and form behavior in `frontend/js/script.js`.
- Replace project samples in the `Selected Work` section in `index.html` and keep their `data-category` values in sync with the filter buttons.
- Update the services by editing the `service-card` articles in `index.html`.
- Replace the starter privacy and terms copy in `privacy.html` and `terms.html` with organization-reviewed content.
- Add original images under `frontend/assets/images` and use descriptive `alt` text if you add `<img>` elements.

## 9. API endpoints

| Method | Endpoint | Purpose |
| --- | --- | --- |
| GET | `/api/health` | API and database health |
| POST | `/api/contact` | Validate and save a contact inquiry |
| POST | `/api/admin/login` | Start an admin session |
| GET | `/api/admin/contacts` | List recent contact inquiries (admin) |
| GET | `/api/admin/contacts/:id` | Read one inquiry (admin) |
| DELETE | `/api/admin/contacts/:id` | Delete an inquiry (admin) |
| POST | `/api/admin/logout` | End the admin session |

## 10. Deploy

Deploy the Node app to a host that supports Node.js and connect it to a managed MySQL database. Configure the environment variables in the host dashboard, import `database/schema.sql`, use HTTPS, set a strong session secret, and configure a persistent session store before production. Set `NODE_ENV=production`. If frontend and API use different origins, set `FRONTEND_ORIGIN` to the exact frontend origin and review the cookie/CORS configuration. Update the placeholder email, business information, privacy and terms pages before launch.

## Troubleshooting

- **`npm` is blocked in PowerShell:** use `npm.cmd`, for example `npm.cmd install`.
- **Database connection error:** confirm MySQL service is running, schema has been imported, and `.env` database settings are correct.
- **Admin login rejected:** check `ADMIN_USERNAME` and regenerate/copy the bcrypt hash with `npm.cmd run admin:hash -- "your-long-admin-password"`.
- **Port already in use:** change `PORT` in `.env` and open that port locally instead.
