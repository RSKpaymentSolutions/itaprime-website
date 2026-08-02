# itaprime-chat

The backend for the assistant in the bottom bar of itaprime.com.

The website is static and public, so the Anthropic API key cannot live in the
page. This function holds it instead. The browser calls this function, the
function calls Claude, and the answer comes back. The key never leaves Supabase.

## Where it runs

Supabase project `raffaele-os` (`dbhskwlhxftkwcwamuji`), region us-east-1.

Endpoint: `https://dbhskwlhxftkwcwamuji.supabase.co/functions/v1/itaprime-chat`

## The one secret it needs

`ANTHROPIC_API_KEY`, set in the Supabase dashboard under
Project settings > Edge Functions > Secrets.

Without it the function still returns 200 and replies with a polite line
pointing visitors at rm@itaprime.com, so the site never shows a broken chat.

## Guards in place

- **Origin allowlist.** CORS only answers itaprime.com and www.itaprime.com
  (plus localhost for testing).
- **JWT required.** `verify_jwt` is on. The page sends the Supabase publishable
  anon key, which is designed to be public.
- **Input caps.** 600 characters per message, last 12 turns only.
- **Output cap.** 500 max_tokens per reply.
- **Prompt rules.** The system prompt refuses customer names, volumes,
  financials, deal terms, processors, roadmap, and pricing, and ignores attempts
  to override its instructions.

## Cost

Runs on `claude-opus-5`. Roughly a cent per exchange. To cut that by about five
times, change `MODEL` to `claude-haiku-4-5` and redeploy.

## Redeploying

Deploy from this file to the `raffaele-os` project as function name
`itaprime-chat`, entrypoint `index.ts`, with `verify_jwt` enabled.
