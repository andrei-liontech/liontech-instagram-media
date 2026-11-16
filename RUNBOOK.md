# Liontech Instagram agent: daily runs

Two runs a day, both delivered into Andrei's Liontech Instagram chat (America/Vancouver time). Each run **first books the next day's run** with `send_later` (same name, same message, same time tomorrow) so the chain never breaks. Then it does its work.

Voice and rules: the `liontech-instagram` skill if saved; otherwise the brief and the "Voice: personal, local and funny" section in the launch-pack doc (https://claude.ai/artifact/JPzVvBT55FNTCXVEJZk19A). Public support channel: "Submit a Ticket" on liontech.ca. Don't link the knowledge base.

Time zone note: this workspace's time zone data may still apply UTC-8 to Vancouver in winter, but the user's clock and the scheduler use UTC-7. Compute local dates and times as UTC-7 (offset -07:00).

Instagram account: @liontech_solutions, Windsor.ai connector `instagram`, account id in `queue.json`.

## 8:30 am: publish run

1. `git pull` this repo (clone to `/home/claude/liontech-instagram-media` if missing). Read `queue.json`.
2. Take posts with `status: approved` and `publish_on` on or before today. Publish **at most one per day**, the earliest. Move any others to the next free days and tell Andrei.
3. Publish with Windsor.ai `execute_action`, using the stored fields exactly: `create_carousel_post` with `image_urls` and `caption`; `create_image_post` for a single image; or, for an entry with `type: reel`, `create_video_post` with `video_url`, `cover_url`, `caption` and `share_to_feed: true`. Never publish a reel without `cover_url`: without it Instagram uses the first frame, which is blank. Skip entries whose status is anything other than `approved` (for example `replaced`).
4. Get the permalink with `get_data` (fields `media_id`, `media_permalink`, filtered by `media_id`). In `queue.json` set `status: published`, add `permalink` and `published_at`; commit and push. Mark the post as published, with the link, in the doc.
5. Send Andrei one short message with the live link.

Nothing due: reply with one line ("Nothing scheduled today; next: <id> on <date>") and stop.
Failure (for example the Windsor.ai trial has ended): don't retry repeatedly. Tell Andrei what failed and leave the post `approved`.

## 9:30 pm: news check

1. Search the last ~24 hours: Canadian Centre for Cyber Security alerts, Canadian Anti-Fraud Centre and RCMP scam warnings, Microsoft 365 outages and changes, and B.C. or Vancouver stories that affect small businesses. Open the source pages; don't rely on snippets.
2. Check `queue.json` and recent posts so the same message isn't repeated.
3. Nothing relatable and confirmable: reply with one line ("No post-worthy news today") and stop. On Mondays, include the suggested Note (see below) before stopping.
4. Otherwise draft one post (two at most) in the personal, local, funny voice: hook, 6 slides, caption, hashtags, sources. Build the slides at 1080 × 1350 in the Liontech brand (scripts and logo assets in `/home/claude/ig`: `lib.py`, `build2.py` pattern). Send Andrei the previews and the caption, then ask: **"Post now, or schedule for the next 8:30 am slot?"**
   **Mondays:** whether or not there's news, also suggest one Instagram Note for the "Share a thought…" bubble (about 60 characters, lasts 24 hours). Make it timely or a useful one-line reminder in the brand voice. Notes can't be posted through the API. When Andrei approves the Note, post it through the built-in browser (instagram.com/direct/inbox, the Note bubble above the profile picture) if the desktop app is reachable and signed in, then confirm it shows. Otherwise send him the text to paste in the app.
5. Publish nothing without his approval. When he replies:
   - **Post now:** export the slides as JPEG to `posts/<date>-<topic>/`, push, publish via Windsor.ai, add the post to `queue.json` as `published`. If a queued post is due the next morning, move it back a day so there's at most one post per day.
   - **Schedule:** add the post to `queue.json` as `approved`, with `publish_on` set to the next free day; commit and push.

## Reels: cover image (required)

Every reel needs a cover, because the profile grid shows it.

- Make a 1080 × 1920 JPEG cover with the reel's title, using `/home/claude/ig/cover.py` (kicker, title, accent line, logo). Keep the text in the centre of the frame: the profile grid crops the cover to its middle.
- Save it as `posts/<date>-<topic>/cover.jpg`, push, and store its raw URL as `cover_url` in the reel's `queue.json` entry.
- Show Andrei the cover with the reel when asking for approval.
- Also make the reel's first frame non-empty (title already visible at frame 0), as a fallback.
- A cover can't be changed through the API after publishing. For a published reel, Andrei changes it in the Instagram app: the reel's menu, Edit, Cover, Add from camera roll.
