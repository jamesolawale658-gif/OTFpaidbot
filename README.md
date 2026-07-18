# OTFpaidbot

Telegram bot deployed via Railway, code hosted on GitHub.

## 1. Create the bot
1. Message **@BotFather** on Telegram
2. `/newbot` → set name and username (`@OTFpaidbot`)
3. Copy the API token it gives you

## 2. Push this code to GitHub
```bash
cd otf-paid-bot
git init
git add .
git commit -m "Initial bot setup"
git branch -M main
git remote add origin https://github.com/<your-username>/otf-paid-bot.git
git push -u origin main
```

## 3. Deploy on Railway
1. Go to railway.app → **New Project** → **Deploy from GitHub repo**
2. Select this repo
3. Go to **Variables** tab and add:
   - `BOT_TOKEN` = your token from BotFather
   - `WEBHOOK_URL` = your Railway public URL (find it under **Settings > Networking > Generate Domain**), e.g. `https://otf-paid-bot-production.up.railway.app`
   - `PAYMENT_PROVIDER_TOKEN` = (optional, only if using /subscribe — get this from BotFather > Payments)
4. Railway will auto-install `requirements.txt` and run the `Procfile` command

## 4. Enable payments (optional)
1. Message **@BotFather** → `/mybots` → select your bot → **Payments**
2. Connect a provider (e.g. Stripe) and copy the provider token
3. Add it as `PAYMENT_PROVIDER_TOKEN` in Railway

## 5. Test it
Message your bot on Telegram:
- `/start` – welcome message
- `/help` – command list
- `/subscribe` – triggers a test invoice (if payments configured)

## Extending it
Replace the `echo` handler in `bot.py` with your actual tool logic (image conversion, word counter, etc.) — that's where your project's core features go.
