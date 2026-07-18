import json
import os
import requests
from telegram import Update
from telegram.ext import ApplicationBuilder, MessageHandler, ContextTypes, filters

# =========================
# TOKEN - your bot token 
# =========================
TOKEN = os.getenv("TELEGRAM_TOKEN")

if not TOKEN:
    raise Exception("TELEGRAM_TOKEN not defined")

# =========================
# OLLAMA - do ollama list and choose what model you want to use
# =========================
OLLAMA_URL = "http://localhost:11434/api/generate"
MODEL = "gemma4:e4b" 

# =========================
# WHITELIST - if you want to give access to specific users
# =========================
ALLOWED_USERS = [user-id,user-id,...]

# =========================
# MEMORY - if you want the ai agent to be able to keep track of your chats, !this will be change with langgraph!
# =========================
MEMORY_FILE = "memory.json"


def load_memory():
    if os.path.exists(MEMORY_FILE):
        with open(MEMORY_FILE, "r") as f:
            return json.load(f)
    return {}


def save_memory(memory):
    with open(MEMORY_FILE, "w") as f:
        json.dump(memory, f, indent=4)


# =========================
# CHAT HANDLER
# =========================
async def chat(update: Update, context: ContextTypes.DEFAULT_TYPE):

    user_id = str(update.message.from_user.id)
    user_text = update.message.text

    # Cleaning - Remove null characters
    user_text = user_text.replace("\x00", "")

    # Input limit
    if len(user_text) > 2000:
        user_text = user_text[:2000]


    # whitelist
    if int(user_id) not in ALLOWED_USERS:
        await update.message.reply_text(" X No autorizado")
        return

    # Memory load
    memory = load_memory()

    # Check user memory
    if user_id not in memory:
        memory[user_id] = {
            "history": []
        }

    if "history" not in memory[user_id]:
        memory[user_id]["history"] = []

    # =========================
    # Context making
    # =========================
    history_text = ""

    for h in memory[user_id]["history"][-10:]:  # últimas 10 interacciones
        history_text += f"Usuario: {h['user']}\nAsistente: {h['assistant']}\n"

    prompt = f"""
"what ever you want your bot to be"

"any other orders you want to give the bot"

History:
{history_text}

Actual user:
{user_text}
"""

    # =========================
    # Ollama call # try with higher timeout times firts to see avg response time of your model in your current hardware
    # =========================
    try:
        response = requests.post(
            OLLAMA_URL,
            json={
                "model": MODEL,
                "prompt": prompt,
                "stream": False
            },
            timeout=120
        )

        response.raise_for_status()
        data = response.json()

        answer = data.get("response", "Error en Ollama")

    except Exception as e:
        answer = f"Error conecting with Ollama: {str(e)}"

    # =========================
    # Save memory
    # =========================
    memory[user_id]["history"].append({
        "user": user_text,
        "assistant": answer
    })

    # limit size
    memory[user_id]["history"] = memory[user_id]["history"][-50:]

    save_memory(memory)

    # =========================
    # respond
    # =========================
    await update.message.reply_text(answer)


# =========================
# BOT INIT
# =========================
app = ApplicationBuilder().token(TOKEN).build()
app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, chat))

print("Bot en marcha...")
app.run_polling()
