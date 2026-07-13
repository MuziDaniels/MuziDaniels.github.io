# Portfolio AI Assistant — Backend

A single serverless function that answers questions about Muzi on his portfolio site, backed by NVIDIA NIM. Deployed separately from the portfolio itself because GitHub Pages can't run backend code.

## Deploy (first time)

1. Push this folder to a new GitHub repo (e.g. `portfolio-ai-backend`), or deploy directly from your machine:
   ```
   npm install -g vercel   # if you don't have it already
   cd this-folder
   vercel
   ```
2. In the Vercel dashboard for this project → **Settings → Environment Variables**, add:
   - `NVIDIA_API_KEY` = the same key from your LiteLLM config
3. Redeploy after adding the env var (`vercel --prod`, or just push again).
4. Vercel will give you a URL like `https://portfolio-ai-backend.vercel.app`. Your endpoint is:
   ```
   https://portfolio-ai-backend.vercel.app/api/chat
   ```
5. Copy that URL into `CHAT_API_URL` in the widget code on the portfolio site (see the frontend snippet).

## Before you go live — check these

- **`ALLOWED_ORIGIN` in `api/chat.js`** is hardcoded to `https://muzidaniels.github.io`. If your live domain is different (custom domain, etc.), update it — otherwise the browser will block the request with a CORS error.
- **Cost control**: `MAX_TOKENS` and `MAX_HISTORY_MESSAGES` in `api/chat.js` are your main defense against a runaway bill if someone spams the widget. The client-side widget also soft-caps messages per session, but that's trivial to bypass by anyone calling the API directly — the server-side caps are what actually matter.
- **Keep the knowledge base current.** The `SYSTEM_PROMPT` in `api/chat.js` is a static snapshot of your bio/projects. When you add a new project to the site, add it here too, or the assistant won't know about it.

## Testing locally

```
vercel dev
```
Then POST to `http://localhost:3000/api/chat` with a body like:
```json
{ "messages": [{ "role": "user", "content": "What does Muzi do at Standard Bank?" }] }
```