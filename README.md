# Aryan AI Cloud

Mobile-first AI cloud starter with:
- AI chat through a secure Vercel serverless endpoint
- Email/password login through Supabase Auth
- Private user file storage (PDF, text, images, ZIP; 10 MB starter limit)
- PDF text extraction in the browser and study prompt shortcuts
- Developer/code help mode
- Basic admin/status screen
- Vercel deployment support

## Important: what is and is not included
This is a starter application. AI chat becomes live only after you add a valid provider API key to Vercel's **server-side** environment variables. The browser never receives `AI_API_KEY`.
File cloud needs a Supabase project and the SQL setup below. Admin is a configuration/status panel; it is not a full multi-user admin console and does not grant cross-user access. Never add an admin password or provider secret to frontend code.

## 1. Create a Supabase project
1. Open https://supabase.com on Android Chrome and create a free project.
2. Open SQL Editor, paste `supabase/schema.sql`, and run it.
3. In Project Settings > API, copy the Project URL and publishable/anon key.
4. In Authentication > URL Configuration, add your Vercel deployment URL to Site URL / Redirect URLs as appropriate.
5. Create `.env.local` locally (or use Vercel Environment Variables) with:
   - `VITE_SUPABASE_URL` = project URL
   - `VITE_SUPABASE_ANON_KEY` = publishable/anon key
   These are public client configuration values; database/storage security relies on RLS policies. Never use a service-role key in the browser.

## 2. Configure AI provider
The default is Groq's OpenAI-compatible endpoint. Create a provider API key on the provider's official site, then in Vercel:
Project > Settings > Environment Variables:
- `AI_PROVIDER` = `groq` (or `openai` / `openrouter`)
- `AI_API_KEY` = your secret key
- `AI_MODEL` = `llama-3.3-70b-versatile` (choose a currently available model in your provider account)
- `ADMIN_EMAIL` = your login email (optional; used only for UI label, not as server-side authorization)

Redeploy after changing environment variables. Free provider plans and model availability may change and can have rate limits; this project cannot promise unlimited free AI.

## 3. Run locally (optional)
Install Node.js on a computer, then:
```bash
npm install
cp .env.example .env.local
npm run dev
```
For API testing locally, use Vercel CLI (`vercel dev`) because Vite alone does not execute `/api` functions.

## 4. Deploy using Android + GitHub + Vercel
1. Create a GitHub repository and upload all files/folders from this ZIP (including `api` and `supabase`).
2. In Vercel, choose Add New > Project, import the repository, Framework Preset: Vite.
3. Add the three `VITE_SUPABASE_*` variables plus server-only `AI_PROVIDER`, `AI_API_KEY`, `AI_MODEL`, and optionally `ADMIN_EMAIL`.
4. Deploy. Open the deployment URL on your Android phone.
5. If you see 404 for `/api/chat`, confirm `api/chat.js` is at repository root, the project is deployed on Vercel, and the latest deployment succeeded.

## 5. Secure setup checklist
- Never paste secret API keys into `src/`, `index.html`, GitHub, or chat.
- If a key was ever exposed publicly, revoke it and create a new one.
- Use a strong password and enable provider MFA.
- Supabase anon/publishable key is designed for client use only with RLS correctly configured.
- Check provider quota and Supabase usage in their dashboards.
- Keep uploads small; do not upload sensitive documents you do not need.

## Current limits
- AI chat: external provider API required; quotas/pricing depend on provider.
- Storage: private Supabase bucket, per-user policies, 10 MB per file in starter schema.
- PDF: text-based PDFs work; scanned/image-only PDFs need OCR, which is not included.
- Admin: configuration/status panel only; no global user management, billing, moderation, or privileged server admin API.
- Chat history: saved in the current browser only; cloud sync is not included yet.
