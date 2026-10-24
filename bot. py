import os, json, telebot
from telebot.types import InlineKeyboardMarkup, InlineKeyboardButton

BOT_TOKEN = os.getenv('BOT_TOKEN')
CHANNEL_ID = "@AllAnimeHindiDub"
CHANNEL_LINK = "https://t.me/AllAnimeHindiDub"

bot = telebot.TeleBot(BOT_TOKEN)
VIDEOS = {}
if os.path.exists("videos.json"):
    try:
        import json as js
        with open("videos.json", 'r') as f:
            VIDEOS = js.load(f)
    except: pass

def save():
    with open("videos.json", 'w') as f:
        json.dump(VIDEOS, f)

def is_joined(user_id):
    try:
        m = bot.get_chat_member(CHANNEL_ID, user_id)
        return m.status in ['member','administrator','creator']
    except:
        return False

@bot.message_handler(commands=['start'])
def start(m):
    parts = m.text.split()
    if len(parts) < 2:
        bot.reply_to(m, "Video ka link se aao")
        return
    key = parts[1].lower()
    if key not in VIDEOS:
        bot.reply_to(m, "Ye video add nahi hai")
        return
    if not is_joined(m.from_user.id):
        markup = InlineKeyboardMarkup()
        markup.add(InlineKeyboardButton("✅ Channel Join Karo", url=CHANNEL_LINK))
        markup.add(InlineKeyboardButton("🔄 Try Again", callback_data=f"try_{key}"))
        bot.send_message(m.chat.id, "Video ke liye channel join karo 👇", reply_markup=markup)
        return
    bot.send_video(m.chat.id, VIDEOS[key])

@bot.callback_query_handler(func=lambda c: c.data.startswith("try_"))
def cb(c):
    key = c.data.split("_")[1]
    if not is_joined(c.from_user.id):
        bot.answer_callback_query(c.id, "Pehle join karo!", show_alert=True)
        return
    bot.send_video(c.message.chat.id, VIDEOS[key])

@bot.message_handler(commands=['add'])
def add(m):
    if not m.reply_to_message or not m.reply_to_message.video:
        bot.reply_to(m, "Kisi video pe reply karke /add naam likho, jaise /add naruto1")
        return
    args = m.text.split()
    if len(args) < 2:
        return
    key = args[1].lower()
    VIDEOS[key] = m.reply_to_message.video.file_id
    save()
    bot.reply_to(m, f"Ho gaya save! Link: https://t.me/{bot.get_me().username}?start={key}")

bot.infinity_polling()
