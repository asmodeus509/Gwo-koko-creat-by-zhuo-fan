# Flex Tupup Backend (ImageKit)

Node.js + Express + PostgreSQL (`pg`) + JWT. ImageKit stores uploaded images and videos. Wallet deposits remain manual: customers submit a MonCash/NatCash transaction reference and an admin confirms/refuses it; the API does not pretend to charge payments automatically.

## Deploy to Render
1. Put the **contents** of this folder at the root of your GitHub repository (so `package.json` is at the repository root). If you keep the folder nested, set Render Root Directory to `flextupup-backend`.
2. Create a PostgreSQL database and set `DATABASE_URL` to its connection string. Set `DATABASE_SSL=true` when required by your database provider.
3. Create a Render Web Service for that repository. Build command: `npm install`; Start command: `npm start`; use Node 20 or newer.
4. In Render > your service > Environment, add all required variables below. Do not commit `.env` or send the private key in chat.
5. Deploy, then open `https://YOUR-SERVICE.onrender.com/health`; it should return JSON with `ok: true`.

## Required environment variables
- `DATABASE_URL`: PostgreSQL connection string.
- `JWT_SECRET`: random secret with at least 32 characters.
- `ADMIN_EMAILS`: comma-separated email addresses allowed to be administrators. Set before the admin registers/logs in.
- `CLIENT_ORIGIN`: exact frontend origin(s), comma-separated; for temporary testing you can use `*`, but use your actual site domain in production.
- `IMAGEKIT_PUBLIC_KEY`: ImageKit public key.
- `IMAGEKIT_PRIVATE_KEY`: ImageKit private key (secret; backend only).
- `IMAGEKIT_URL_ENDPOINT`: ImageKit URL endpoint, e.g. `https://ik.imagekit.io/YOUR_IMAGEKIT_ID`.

Optional: `DATABASE_SSL=true`, `NODE_ENV=production`, `MONCASH_NUMBER`, `NATCASH_NUMBER`, `WHATSAPP_NUMBER`, `MAX_UPLOAD_MB` (default 80 MB; actual ImageKit plan limits still apply).

## ImageKit setup
In ImageKit dashboard, find API keys and URL endpoint under Developer options / API keys (wording may vary). Add the three `IMAGEKIT_*` variables in Render Environment. Never put `IMAGEKIT_PRIVATE_KEY` in HTML/frontend. If a key was exposed publicly, regenerate it.

## Upload routes
- `POST /api/admin/tutorial/video` multipart field `video` (video tutorial upload)
- `POST /api/admin/upload` multipart field `file` (general media)
- `POST /api/admin/ads/upload` multipart image field (advertisements)
- `PUT /api/admin/media` accepts image data URLs and sends them to ImageKit
- `POST /api/admin/ads` and `PATCH /api/admin/ads/:id` accept HTTPS image URLs or image data URLs

Uploads require a valid admin JWT, and the email must be listed in `ADMIN_EMAILS`. All media URLs are stored in PostgreSQL or returned to the frontend. The server does not fake payment verification.

## Important
This package has been adapted for ImageKit but cannot be proven live until you deploy it with a real PostgreSQL database and your own ImageKit credentials. ImageKit free-tier quotas and upload limits apply. Existing database tables are created non-destructively with `CREATE TABLE IF NOT EXISTS` / `ALTER TABLE ... ADD COLUMN IF NOT EXISTS`.


## Render deployment (important)
Upload the contents of this folder to the ROOT of the GitHub repository. The repository root must contain `package.json`, `render.yaml`, and the `src/` folder. In Render, leave Root Directory blank, use Build Command `npm install`, and Start Command `npm start`. Image uploads use ImageKit; configure `IMAGEKIT_PUBLIC_KEY`, `IMAGEKIT_PRIVATE_KEY`, and `IMAGEKIT_URL_ENDPOINT` in Render Environment. The service also requires `DATABASE_URL`, `JWT_SECRET` (at least 32 characters), and `ADMIN_EMAILS` (the exact admin email).
