
import telebot
import random

TOKEN = 'توکن_ربات_اینجا'
bot = telebot.TeleBot(TOKEN)

games = {
    'س،ا،ل،م': ['سلام', 'سال', 'مس', 'سل', 'سم', 'مساله'],
    'ر،ف،ا،ن': ['ران', 'رف', 'نفر', 'رفتن', 'فران']
}

user_data = {}

@bot.message_handler(commands=['start'])
def send_welcome(message):
    bot.reply_to(message, "🎮 خوش اومدی به بازی حدس کلمه!
برای شروع بنویس: /play")

@bot.message_handler(commands=['play'])
def new_game(message):
    user_id = message.chat.id
    letters, words = random.choice(list(games.items()))
    user_data[user_id] = {
        'letters': letters,
        'answers': words,
        'found': []
    }
    bot.send_message(user_id, f"🧩 با این حروف کلمه بساز:
🔤 {letters}")

@bot.message_handler(func=lambda m: True)
def check_word(message):
    user_id = message.chat.id
    text = message.text.strip()
    data = user_data.get(user_id)

    if not data:
        bot.send_message(user_id, "برای شروع بازی، دستور /play رو بزن.")
        return

    if text in data['answers']:
        if text in data['found']:
            bot.send_message(user_id, f"⚠️ '{text}' رو قبلاً گفتی!")
        else:
            data['found'].append(text)
            bot.send_message(user_id, f"✅ آفرین! '{text}' درسته.")
    else:
        bot.send_message(user_id, f"❌ '{text}' توی لیست نیست.")

    if set(data['found']) == set(data['answers']):
        bot.send_message(user_id, "🎉 تبریک! همه کلمه‌ها رو پیدا کردی!
برای شروع بازی جدید بنویس: /play")

bot.infinity_polling()
