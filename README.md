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

- **DMs:** Open the bot and send `/start`. It replies to text, captions, photos,
  and voice notes with a text reply. Voice notes are transcribed with Groq's
  Whisper model, so voice-note replies require `MODEL_PROVIDER=groq` and a
  valid `GROQ_API_KEY`.
- **Groups:** Add the bot, then type its display name (for example, `Pino`),
  mention its username, or reply to one of its messages. To receive messages
  containing only its display name, disable group privacy in @BotFather:
  send `/setprivacy`, select this bot, choose **Disable**, then remove and
  re-add the bot to the group so Telegram applies the setting.
  Bots with privacy disabled receive all group messages, but this bot only
  replies when its name or username is mentioned, someone replies to one of
  its messages, or it sees a supported channel forward.
  For photo banter, tag the bot in the photo caption or reply to one of its
  messages with the photo. It does not retrieve profile pictures or inspect
  group photos unless the message addresses it.
  To get a voice-note reply, reply to one of the bot's messages with the voice
  note, or add its name in the voice note's caption.
  If it does not reply, make sure it is running and that group privacy is
  disabled for display-name triggers. Both `@username` mentions and
  Telegram's selected-name mentions are supported.
- **Channels:** Make the bot an admin with permission to post messages. It
  replies to channel posts when the channel has no linked discussion group.
- **Channel comments:** Link a discussion group to the channel and add the bot
  to both. It responds to the channel post's automatic forward in the group.
  Make it an admin in the discussion group if Telegram's privacy settings
  prevent it from seeing those forwards.

The bot keeps the latest six exchanges in memory for each chat so it can follow
up on earlier messages. History is cleared when the bot restarts. Text and any
photo attached to a message that addresses the bot are sent to the configured
model to generate a reply. Voice notes are sent to Groq for transcription before
their text is passed to the chat model; use `GROQ_TRANSCRIPTION_MODEL` to
override its default `whisper-large-v3-turbo`. Twi transcription quality may
vary. With the default local Ollama setup, text and photos are processed on the
same PC; voice-note transcription still requires Groq. Photos are limited to
5 MB and voice notes to 20 MB.