# bot.py
import os
import time
import random
import requests
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, ContextTypes, MessageHandler, filters

# === 🔐 CONFIGURAÇÕES ===
TOKEN = "8100881437:AAG7AfibNp1z_y4gKIJAbN5XOZZgAL380fA"  # ← Coloque o token do seu bot do @BotFather
ALVO = "brumadodeacucar"   # ← Nome de usuário do alvo

# === 📱 HEADERS REALISTAS ===
HEADERS = {
    "User-Agent": "Instagram 217.0.0.12.119 Android (30/11; 520dpi; 1242x2688; samsung; SM-G973F; dreamlte; exynos9820; pt_BR; 336274564)",
    "X-IG-App-ID": "1217981644879628",
    "Content-Type": "application/x-www-form-urlencoded; charset=UTF-8"
}

# === 🚨 DENUNCIAR USUÁRIO ===
def report_account(sessionid, username, user_id, reason="1"):
    headers = HEADERS.copy()
    headers["Cookie"] = f"sessionid={sessionid}"
    
    data = {
        "reason_id": reason,
        "source_name": "",
        "frx_context": ""
    }
    try:
        response = requests.post(
            f"https://i.instagram.com/api/v1/users/{user_id}/flag_user/",
            headers=headers,
            data=data,
            timeout=10
        )
        return response.status_code == 200
    except:
        return False

# === 🔍 OBTER USER_ID ===
def get_user_id(sessionid, username):
    headers = HEADERS.copy()
    headers["Cookie"] = f"sessionid={sessionid}"
    try:
        response = requests.get(
            f"https://i.instagram.com/api/v1/users/web_profile_info/?username={username}",
            headers=headers,
            timeout=10
        )
        if response.status_code == 200:
            data = response.json()
            return data["data"]["user"]["id"]
        return None
    except:
        return None

# === 🤖 COMANDOS DO BOT ===
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        f"🇧🇷 Olá! Este bot ajuda a denunciar @{ALVO}\n\n"
        "⚠️ Atenção: NÃO envie seu sessionid aqui se não confiar.\n\n"
        "👉 Envie /guia para aprender a denunciar com seu celular.\n"
        "👉 Envie /denunciar para tentar denúncia no servidor (menos seguro)."
    )

async def guia(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "📌 PASSO A PASSO PARA DENUNCIAR:\n\n"
        "1️⃣ Abra o Instagram e faça login com uma conta secundária\n"
        "2️⃣ Acesse: https://seu-usuario.github.io/ig-report-tool/\n"
        "3️⃣ Clique em 'Obter sessionid'\n"
        "4️⃣ Copie o código que aparecer\n"
        "5️⃣ Instale o Termux no Android\n"
        "6️⃣ Cole este comando:\n"
        "   `curl -s https://raw.githubusercontent.com/seu-usuario/ig-report-tool/main/install.sh | bash`\n\n"
        "✅ Pronto! O script rodará sozinho com intervalos aleatórios."
    )

async def denunciar(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "🔐 Envie seu sessionid para iniciar as denúncias (use uma conta secundária!)\n"
        "❗ Não envie se não confiar. O sessionid é como uma senha."
    )
    context.user_data['esperando_sessionid'] = True

async def handle_message(update: Update, context: ContextTypes.DEFAULT_TYPE):
    if not context.user_data.get('esperando_sessionid'):
        return

    sessionid = update.message.text.strip()
    if len(sessionid) < 30:
        await update.message.reply_text("❌ sessionid inválido. Envie um válido.")
        return

    await update.message.reply_text("✅ Iniciando denúncias automáticas...")

    user_id = get_user_id(sessionid, ALVO)
    if not user_id:
        await update.message.reply_text("❌ Falha ao obter ID do alvo. Tente novamente mais tarde.")
        return

    await update.message.reply_text(f"🆔 ID do alvo: {user_id}\n⏳ Enviando denúncias a cada 3-10 minutos...")

    for _ in range(50):  # Máximo de 50 denúncias por sessão
        reason = str(random.choice([1, 6, 5]))
        if report_account(sessionid, ALVO, user_id, reason):
            await update.message.reply_text(f"[✓] Denúncia enviada! Motivo: {reason}")
        else:
            await update.message.reply_text("[✗] Falha ao denunciar. Seu sessionid pode ter expirado.")

        wait_time = random.uniform(180, 600)
        time.sleep(wait_time / 10)  # Acelerado para teste

    await update.message.reply_text("✅ Fim da sessão de denúncias.")

# === 🚀 INICIAR BOT ===
if __name__ == "__main__":
    app = ApplicationBuilder().token(TOKEN).build()

    app.add_handler(CommandHandler("start", start))
    app.add_handler(CommandHandler("guia", guia))
    app.add_handler(CommandHandler("denunciar", denunciar))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_message))

    print("Bot rodando...")
    app.run_polling()
