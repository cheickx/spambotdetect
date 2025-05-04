from telegram import Update
from telegram.ext import ApplicationBuilder, ContextTypes, MessageHandler, filters
import re
import os

# 🔐 Récupère le token depuis les variables d’environnement (idéal pour Railway)
TOKEN = os.environ.get("7983605943:AAE_Bg3BwFa856LF9IPCouaEDPbkFhIaZ1s")

# 🔗 Supprime les messages contenant des liens (sauf admin)
async def delete_links(update: Update, context: ContextTypes.DEFAULT_TYPE):
    message = update.message
    if not message:
        return

    user_id = message.from_user.id
    chat_id = message.chat.id

    # Récupère les admins du groupe
    admins = await context.bot.get_chat_administrators(chat_id)
    admin_ids = [admin.user.id for admin in admins]

    # Si ce n’est pas un admin et le message contient un lien, on supprime
    if user_id not in admin_ids:
        if re.search(r'(http[s]?://|www\.|t\.me/|bit\.ly|discord\.gg)', message.text or '', re.IGNORECASE):
            await message.delete()

# 🟢 Lancer le bot
async def main():
    app = ApplicationBuilder().token(TOKEN).build()
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, delete_links))
    await app.run_polling()

if __name__ == '__main__':
    import asyncio
    asyncio.run(main())
