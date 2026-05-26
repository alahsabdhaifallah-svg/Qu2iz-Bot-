pyTelegramBotAPI==4.14.0
# قاموس الأسئلة: المفتاح هو السؤال، والقيمة هي الإجابة الصحيحة
questions = {
    "ما هي عاصمة فرنسا؟": "باريس",
    "كم عدد الكواكب في المجموعة الشمسية؟": "8",
    "من هو مخترع المصباح الكهربائي؟": "توماس إديسون",
    "ما هو أطول نهر في العالم؟": "النيل",
    "كم عدد الدول العربية؟": "22"
}
import telebot
import random
from questions import questions

TOKEN = 'ضع_توكن_البوت_هنا'  # ⚠️ سنستبدل هذا بمتغير بيئة لاحقاً
bot = telebot.TeleBot(TOKEN)

user_scores = {}
user_current_question = {}

# أمر البدء
@bot.message_handler(commands=['start'])
def start(message):
    markup = telebot.types.ReplyKeyboardMarkup(resize_keyboard=True)
    btn_start = telebot.types.KeyboardButton("ابدأ المسابقة")
    btn_score = telebot.types.KeyboardButton("نتيجتي")
    markup.add(btn_start, btn_score)
    bot.send_message(message.chat.id, "أهلاً بك في بوت المسابقة! اضغط على الزر للبدء.", reply_markup=markup)

# معالج النصوص من الأزرار
@bot.message_handler(func=lambda message: True)
def handle_message(message):
    chat_id = message.chat.id
    text = message.text

    if text == "ابدأ المسابقة":
        start_quiz(chat_id)
    elif text == "نتيجتي":
        show_score(chat_id)
    elif chat_id in user_current_question:
        check_answer(chat_id, text)
    else:
        bot.send_message(chat_id, "الرجاء استخدام الأزرار الظاهرة.")

def start_quiz(chat_id):
    user_scores[chat_id] = 0
    send_question(chat_id)

def send_question(chat_id):
    q, a = random.choice(list(questions.items()))
    user_current_question[chat_id] = a
    bot.send_message(chat_id, f"❓ {q}")

def check_answer(chat_id, user_answer):
    correct_answer = user_current_question.pop(chat_id, None)
    if not correct_answer:
        return

    if user_answer.strip().lower() == correct_answer.strip().lower():
        user_scores[chat_id] = user_scores.get(chat_id, 0) + 1
        bot.send_message(chat_id, "✅ إجابة صحيحة! أحسنت.")
    else:
        bot.send_message(chat_id, f"❌ إجابة خاطئة. الإجابة الصحيحة هي: {correct_answer}")

    # عرض السؤال التالي تلقائياً
    send_question(chat_id)

def show_score(chat_id):
    score = user_scores.get(chat_id, 0)
    bot.send_message(chat_id, f"🏆 نتيجتك الحالية: {score} نقاط.")

print("البوت يعمل بنجاح...")
bot.infinity_polling()
import os
TOKEN = os.environ.get('8813412939:AAFwPMfT9w1Yia09GvwE0-fgcS23JWNysvU')
