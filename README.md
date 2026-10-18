# Manace Telegram Bot

A Telegram bot that chats in DMs, joins group conversations when mentioned or
replied to, and reacts to channel posts or posts forwarded into a linked
discussion group. Replies use a playful, argumentative persona that blends
Ghanaian banter, sharp roasts, and detailed, evidence-led debate when the
conversation calls for it. By default, a vision-capable model runs locally
through Ollama.

## Setup

1. Create a bot with [@BotFather](https://t.me/BotFather) and copy its token.
2. Install [Ollama for Windows](https://ollama.com/download), then download the
  local vision model:

   ```powershell
   ollama pull qwen2.5vl:3b
   ```

3. Copy `.env.example` to `.env` and set `TELEGRAM_BOT_TOKEN`. Never commit
  `.env`. The default model runs locally and does not use a hosted API quota.
4. Install the Python dependencies and run the bot:

   ```powershell
   python -m venv .venv
   .\.venv\Scripts\Activate.ps1
   pip install -r requirements.txt
   python main.py
   ```

Keep Ollama running while the bot is running. Local response speed depends on
the PC's hardware. To use Gemini instead, set `MODEL_PROVIDER=gemini` and
configure `GEMINI_API_KEY` in `.env`; hosted providers still have usage limits.

## Deploying on Render

When `RENDER_EXTERNAL_URL` is present, the app receives Telegram updates through
a webhook instead of polling. This lets a Telegram update reach the Render web
service and wake a free instance automatically. The first reply after the
service has been idle may take about a minute. No separate keep-alive service
is needed. Avoid running a local copy with the same bot token while the Render
deployment is active.

## Add it to chats

- **DMs:** Open the bot and send `/start`. It replies to text, captions, and
  photos.
- **Groups:** Add the bot, then mention it or reply to one of its messages.
  If the bot only responds when it is an admin, open [@BotFather](https://t.me/BotFather),
  send `/setprivacy`, select this bot, and choose **Disable**. Then remove the
  bot from the group and add it again so Telegram applies the setting. Bots
  with privacy disabled receive all group messages, but this bot's code still
  only replies when it is mentioned, replied to, or sees a supported channel
  forward.
  For photo banter, tag the bot in the photo caption or reply to one of its
  messages with the photo. It does not retrieve profile pictures or inspect
  group photos unless the message addresses it.
  If a mention gets no reply, make sure you tagged this bot's actual Telegram
  account (not just its display name) and that the bot is still running. Both
  `@username` mentions and Telegram's selected-name mentions are supported.
- **Channels:** Make the bot an admin with permission to post messages. It
  replies to channel posts when the channel has no linked discussion group.
- **Channel comments:** Link a discussion group to the channel and add the bot
  to both. It responds to the channel post's automatic forward in the group.
  Make it an admin in the discussion group if Telegram's privacy settings
  prevent it from seeing those forwards.

The bot does not keep conversation history. Text and any photo attached to a
message that addresses the bot are sent to the configured model to generate a
reply. With the default local Ollama setup, these are processed on the same PC.
Photos are limited to 5 MB.