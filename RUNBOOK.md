# Liontech Instagram agent: daily runs

Two runs a day, both delivered into Andrei's Liontech Instagram chat (America/Vancouver time). Each run **first books the next day's run** with `send_later` (same name, same message, same time tomorrow) so the chain never breaks. Then it does its work.

Voice and rules: the `liontech-instagram` skill if saved; otherwise the brief and the "Voice: personal, local and funny" section in the launch-pack doc (https://claude.ai/artifact/JPzVvBT55FNTCXVEJZk19A). Public support channel: "Submit a Ticket" on liontech.ca. Don't link the knowledge base.

Instagram account: @liontech_solutions, Windsor.ai connector `instagram`, account id in `queue.json`.

## 8:30 am: publish run

1. `git pull` this repo (clone to `/home/claude/liontech-instagram-media` if missing). Read `queue.json`.
2. Take posts with `status: approved` and `publish_on` on or before today. Publish **at most one per day**, the earliest. Move any others to the next free days and tell Andrei.
3. Publish with Windsor.ai `execute_action`: `create_carousel_post` (or `create_image_post` for one image), using the stored `image_urls` and `caption` exactly.
4. Get the permalink with `get_data` (fields `media_id`, `media_permalink`, filtered by `media_id`). In `queue.json` set `status: published`, add `permalink` and `published_at`; commit and push. Mark the post as published, with the link, in the doc.
5. Send Andrei one short message with the live link.

Nothing due: reply with one line ("Nothing scheduled today; next: <id> on <date>") and stop.
Failure (for example the Windsor.ai trial has ended): don't retry repeatedly. Tell Andrei what failed and leave the post `approved`.

## 9:30 pm: news check

1. Search the last ~24 hours: Canadian Centre for Cyber Security alerts, Canadian Anti-Fraud Centre and RCMP scam warnings, Microsoft 365 outages and changes, and B.C. or Vancouver stories that affect small businesses. Open the source pages; don't rely on snippets.
2. Check `queue.json` and recent posts so the same message isn't repeated.
3. Nothing relatable and confirmable: reply with one line ("No post-worthy news today") and stop.
4. Otherwise draft one post (two at most) in the personal, local, funny voice: hook, 6 slides, caption, hashtags, sources. Build the slides at 1080 × 1350 in the Liontech brand (scripts and logo assets in `/home/claude/ig`: `lib.py`, `build2.py` pattern). Send Andrei the previews and the caption, then ask: **"Post now, or schedule for the next 8:30 am slot?"**
   **Mondays:** whether or not there's news, also suggest one Instagram Note for the "Share a thought…" bubble (about 60 characters, lasts 24 hours). Make it timely or a useful one-line reminder in the brand voice. Notes can't be posted through the API, so Andrei pastes it in the app himself.
5. Publish nothing without his approval. When he replies:
   - **Post now:** export the slides as JPEG to `posts/<date>-<topic>/`, push, publish via Windsor.ai, add the post to `queue.json` as `published`. If a queued post is due the next morning, move it back a day so there's at most one post per day.
   - **Schedule:** add the post to `queue.json` as `approved`, with `publish_on` set to the next free day; commit and push.
