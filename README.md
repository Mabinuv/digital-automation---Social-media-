# Social Automation – n8n workflow (template)

Daily n8n workflow: reads a brief from Google Sheets → analyses two reference posts with Gemini → generates an image (Gemini image model, optional OpenAI branch) → writes Instagram + Facebook captions → emails you a preview with **Approve / Regenerate** links → on approval, schedules one image post per day on Instagram and Facebook via Blotato.

## Flow
Schedule Trigger → Read brief (Sheet) → Download refs/logo (Drive) → Gemini style profile → image generation → upload to Drive → Gemini captions → write back to Sheet → preview email → **Wait (webhook resume)** → Approve → Blotato upload + schedule IG & FB. Regenerate resets the row to `pending` and re-triggers the workflow via its production webhook. Failures go to a separate error workflow that emails an alert.

## Setup
1. Import `social-automation.n8n.json` into n8n (inactive on import).
2. Create credentials and attach them to the nodes (all credentials were stripped): Google Sheets OAuth2, Google Drive OAuth2, Gmail OAuth2, Gemini/Google AI API key, OpenAI API key (only if using that branch), Blotato API, and a **Header Auth** credential for the Webhook node.
3. Replace placeholders (search the JSON/nodes for `YOUR_` and `you@example.com`):

| Placeholder | What to put |
|---|---|
| `YOUR_GOOGLE_SHEET_ID` | ID of your brief sheet |
| `YOUR_DRIVE_FOLDER_ID` | Drive folder for generated images (share "anyone with link" if Blotato fetches it) |
| `YOUR_INSTAGRAM_ACCOUNT_ID` / `YOUR_FACEBOOK_ACCOUNT_ID` | Blotato account IDs |
| `YOUR_FACEBOOK_PAGE_ID` | Facebook page ID |
| `YOUR_WEBHOOK_PATH` | Your webhook path (the production URL used by Regenerate) |
| `YOUR_APP_URL`, `YOUR_DOMAIN`, `YOUR_BRAND` | Your site, domain, brand name |
| `you@example.com` | Preview and alert recipient |
| `YOUR_DEFAULT_*`, `YOUR_BRAND_*`, etc. | Brand voice, audience, tone, CTA used in the Gemini prompts |

4. Set the n8n host in the regenerate HTTP Request URL, set the workflow timezone, and choose an error workflow in Settings.
5. Test manually, then activate.

## Sheet columns
`post#`, `date`, `posting_date_IST`, `posting_date_UTC`, `status` (`pending` → `done`), `primary_subject`, `reference_post_1`, `reference_post_2`, `logo_url`, plus the brief fields your prompts use and output columns written back (`instagram_caption`, `facebook_caption`, image URL, error log). Check the Sheets nodes for the exact names.
