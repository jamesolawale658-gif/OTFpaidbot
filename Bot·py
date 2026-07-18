import os
import logging
from telegram import Update, LabeledPrice
from telegram.ext import (
    Application,
    CommandHandler,
    ContextTypes,
    PreCheckoutQueryHandler,
    MessageHandler,
    filters,
)

# ── Config ──────────────────────────────────────────────
BOT_TOKEN = os.environ["BOT_TOKEN"]          # set in Railway env vars
WEBHOOK_URL = os.environ.get("WEBHOOK_URL")  # e.g. https://your-app.up.railway.app
PORT = int(os.environ.get("PORT", 8080))
PAYMENT_PROVIDER_TOKEN = os.environ.get("PAYMENT_PROVIDER_TOKEN", "")  # from BotFather /mybots > Payments

logging.basicConfig(
    format="%(asctime)s - %(name)s - %(levelname)s - %(message)s",
    level=logging.INFO,
)
logger = logging.getLogger(__name__)


# ── Handlers ────────────────────────────────────────────
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "Welcome to OTFpaidbot!\n\n"
        "Commands:\n"
        "/help – list commands\n"
        "/subscribe – buy premium access\n"
    )


async def help_command(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "Available commands:\n"
        "/start – welcome message\n"
        "/subscribe – buy premium access\n"
    )


async def echo(update: Update, context: ContextTypes.DEFAULT_TYPE):
    # Placeholder for your actual tool logic (image conversion, word count, etc.)
    await update.message.reply_text(f"You said: {update.message.text}")


# ── Payments (Telegram Payments API) ────────────────────
async def subscribe(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if not PAYMENT_PROVIDER_TOKEN:
        await update.message.reply_text(
            "Payments aren't configured yet. Set PAYMENT_PROVIDER_TOKEN in Railway."
        )
        return

    chat_id = update.effective_chat.id
    title = "Premium Access"
    description = "Unlock premium features for 30 days"
    payload = "premium-30d"
    currency = "USD"
    price = LabeledPrice("Premium (30 days)", 500)  # 500 = $5.00 (amount in cents)

    await context.bot.send_invoice(
        chat_id=chat_id,
        title=title,
        description=description,
        payload=payload,
        provider_token=PAYMENT_PROVIDER_TOKEN,
        currency=currency,
        prices=[price],
    )


async def precheckout_callback(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.pre_checkout_query
    if query.invoice_payload != "premium-30d":
        await query.answer(ok=False, error_message="Something went wrong.")
    else:
        await query.answer(ok=True)


async def successful_payment_callback(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("Payment received! Premium access unlocked.")
    # TODO: mark user as premium in your database


# ── App setup ────────────────────────────────────────────
def main():
    app = Application.builder().token(BOT_TOKEN).build()

    app.add_handler(CommandHandler("start", start))
    app.add_handler(CommandHandler("help", help_command))
    app.add_handler(CommandHandler("subscribe", subscribe))
    app.add_handler(PreCheckoutQueryHandler(precheckout_callback))
    app.add_handler(MessageHandler(filters.SUCCESSFUL_PAYMENT, successful_payment_callback))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, echo))

    if WEBHOOK_URL:
        # Webhook mode — recommended for Railway
        app.run_webhook(
            listen="0.0.0.0",
            port=PORT,
            url_path=BOT_TOKEN,
            webhook_url=f"{WEBHOOK_URL}/{BOT_TOKEN}",
        )
    else:
        # Polling mode — fallback for local testing
        app.run_polling()


if __name__ == "__main__":
    main()
