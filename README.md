from telegram import Update, Poll, InlineKeyboardButton, InlineKeyboardMarkup, ReplyKeyboardMarkup, KeyboardButton
from telegram.ext import ApplicationBuilder, CommandHandler, ContextTypes, CallbackQueryHandler, MessageHandler, filters
import random
import time

# توکن را وارد کنید
TOKEN = "7630407279:AAEC0NQh7DoyowLUBZ7hmjntOhm0lDoHNpY"

questions = [
    {
        "id": 1,
        "question": "طبق روایت پیامبر صلی الله علیه و آله چه فردی دیوانه است؟",
        "options": ["متکبر", "حسود", "منافق", "ریاکار"],
        "correct_option_id": 0
    },
    {
        "id": 2,
        "question": "کدام واژه اشاره‌ به پاکسازی دارد؟",
        "options": ["شفا", "رحمت", "احسان", "تقوا"],
        "correct_option_id": 0
    },
    {
        "id": 3,
        "question": "........داروی شفا بخشی است که هیچ بیماری از آن برنمی خیزد",
        "options": ["نماز", "دعا", "قرآن", "عقل"],
        "correct_option_id": 2
    },
    {
        "id": 4,
        "question": "کدام یک از کارهای زیر موجب بی اعتمادی مردم نسبت به یکدیگر می شود؟",
        "options": ["ترک نماز", "خیانت در امانت", "دنیا پرستی", "دوری از ذکر خدا"],
        "correct_option_id": 1
    },
    {
        "id": 5,
        "question": "طبق آیه قرآن کدام کار به تقوا نزدیک تر است؟",
        "options": ["دانش افزایی", "دعوت به وحدت", "عدالت", "تعدیل و ثروت"],
        "correct_option_id": 2
    },
    {
        "id": 6,
        "question": "عبارت «انسان مؤمن دل را در معرض نسیمهای بهاری قرآن قرار میدهد. با همه وجود خود را در فضای نزول قرآن می بیند آیه ها بی واسطه با او سخن می گوید ..... به چه مطلبی اشاره دارد؟",
        "options": ["تدبر", "قرائت", "تلاوت", "بطن"],
        "correct_option_id": 0
    },
    {
        "id": 7,
        "question": "عبور از سطوح اولیه و مفاهیم ظاهری آیات و ورود اندیشمندانه به لایه های ژرف برای کشف حقایق پنهانی چه نامیده می شود؟",
        "options": ["تفسیر", "تدبر", "طهارت", "فقه"],
        "correct_option_id": 1
    },
    {
        "id": 8,
        "question": "آگاه باشید که قرائت قرآن هنگامی که در آن نباشد هیچ خبری ندارد»",
        "options": ["تفسير", "تفقه", "تأويل", "تدبر"],
        "correct_option_id": 3
    },
    {
        "id": 9,
        "question": "قرآن از کدام شناخت بی نیاز است؟",
        "options": ["تحلیلی", "ریشه ای", "سندی", "تفسیری"],
        "correct_option_id": 2
    },
    {
        "id": 10,
        "question": "اینکه قرآن مشتمل بر چه مسائلی است و آن مسائل چگونه و با چه سبکی عرضه شده است و طرح قرآن برای رساندن آدمیان به سعادت چیست به کدام یک از انواع شناخت اشاره دارد؟",
        "options": ["شناخت سندی", "شناخت ریشه ای", "شناخت تحلیلی", "شناخت اصالت الهی"],
        "correct_option_id": 2
    },
    {
        "id": 11,
        "question": "حضرت علی علیه السلام چه چیزی را از بزرگترین دردهای بشری معرفی نموده است؟",
        "options": ["مفاسد اجتماعی", "کفر و گمراهی", "بی اعتقادی", "گناه کبیره"],
        "correct_option_id": 1
    },
    {
        "id": 12,
        "question": "حدیث: «حسن ظن ما ظن ما به راحتی دل و سلامت دین است از کدام معصوم نقل شده است؟",
        "options": ["حضرت علی (ع)", "امام حسین (ع)", "امام حسن (ع)", "پیامبر اکرم (ص)"],
        "correct_option_id": 0
    },
    {
        "id": 13,
        "question": "مقصود از علم در آیه «هل يستوى الذين يعلمون و الذین لا یعلمون» چیست؟",
        "options": ["علم اخلاق", "علم تجربی", "مطلق علم و دانش", "علم دین"],
        "correct_option_id": 2
    },
    {
        "id": 14,
        "question": "خداباوری به عنوان نخستین و به اقتضای کدام گر دوش همه آدمها گذاشته شده است؟",
        "options": ["دین", "عقل", "وجدان درونی", "همه موارد"],
        "correct_option_id": 3
    },
    {
        "id": 15,
        "question": "غایت و نهایت جهان بر اساس آیات چیست؟",
        "options": ["مجازات کفار", "برپایی رستاخیز و نظام عدل", "افرینش جهان", "کمال انسان"],
        "correct_option_id": 1
    },
    {
        "id": 16,
        "question": "کدام دسته از مباحث علمی در قرآن مطرح شده که برخی از آنها بخاطر بی خبری دانشمندان تا صد سال بعد نیز مورد قبول عام مراکز علمی جهان نبود؟",
        "options": ["زمین شناسی", "علوم پزشکی", "کیهان شناسی", "همه موارد"],
        "correct_option_id": 3
    },
    {
        "id": 17,
        "question": "ام الكتاب وصف کدام یک از سوره های قرآن می باشد ؟",
        "options": ["توحید", "ياسين", "اسراء", "حمد"],
        "correct_option_id": 3
    },
    {
        "id": 18,
        "question": "قرآن به صورت تدریجی طی چند سال نازل شد ؟",
        "options": ["23 سال", "13 سال", "40 سال", "25 سال"],
        "correct_option_id": 0
    },
    {
        "id": 19,
        "question": "در مورد تعیین شب قدر بیشترین تکیه شیعه و سنی بر چه شبی می باشد ؟",
        "options": ["شیعه بر شب بیست و سوم سنی بر شب نوزدهم", "سنی بر شب بیست و سوم شیعه بر شب نوزدهم", "شیعه بر شب بیست و سوم سنی بر شب بیست و هفتم", "شیعه بر شب بیست و سوم سنی بر شب بیست و نهم"],
        "correct_option_id": 2
    },
    {
        "id": 20,
        "question": "مقصود از این سبیل در آیات قرآن چیست ؟",
        "options": ["بچه سر راهی", "در راه ماندگان", "بچه های در راه مانده", "مطلق فقرا"],
        "correct_option_id": 1
    },
    {
        "id": 21,
        "question": "مقصود از شفاعت حسنه در آيه ومن يشفع شفاعه حسنه ... » چیست؟",
        "options": ["شفاعت پیامبر در فردای قیامت", "تشویق مومنان به اعمال خیر", "شفاعت در برزخ", "تشویق پیامبر به جهاد"],
        "correct_option_id": 3
    },
    {
        "id": 22,
        "question": "کدام عمل در روز رستاخیز به شکل ظلمت و تاریکی مجسم می شود ؟",
        "options": ["ظلم", "حق الناس", "تكبر", "دروغ"],
        "correct_option_id": 0
    },
    {
        "id": 23,
        "question": "بر اساس روایت منقول از پیامبر گرامی اسلام ترک ازدواج از ترس فقر چه . حکمی دارد ؟",
        "options": ["معقول و مناسب", "بد گمانی به خدا است.", "مایه عذاب است.", "موجب اعتماد به خدا است."],
        "correct_option_id": 1
    },
    {
        "id": 24,
        "question": "پیامبر خدا رهبانیت است خود را در چه چیزی قرار داد ؟",
        "options": ["خدمت به خلق خدا", "بندگی خدا", "روزه ماه رمضان", "جهاد در راه خدا"],
        "correct_option_id": 3
    },
    {
        "id": 25,
        "question": "مقصود از ایامی در آیه وانکحو الایامی» چیست ؟",
        "options": ["زنان مجرد", "زنان و مردان مجرد", "روزهای سال", "مردان مجرد"],
        "correct_option_id": 1
    },
    {
        "id": 26,
        "question": "صدقات و نحله در آیه و آتو النساء صدقاتهن نحله به ترتیب به چه معنی می باشند ؟",
        "options": ["عطيه - صدقه", "مهریه - بدهی و دین", "بخشش - بدهی", "مهریه - مهریه"],
        "correct_option_id": 1
    }
]

    





async def send_poll_with_retry(context, chat_id, question, options, correct_option_id, retries=5):
    for attempt in range(retries):
        try:
            poll = await context.bot.send_poll(
                chat_id=chat_id,
                question=question,
                options=options,
                type=Poll.QUIZ,
                correct_option_id=correct_option_id,
                is_anonymous=False
            )
            return poll
        except Exception as e:
            if attempt < retries - 1:
                time.sleep(2)  # wait for 2 seconds before retrying
            else:
                raise e

async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_first_name = update.effective_user.first_name
    welcome_message = f"سلام {user_first_name}، خوش آمدی! لطفاً یکی از گزینه‌ها را انتخاب کن:"
    main_menu = ReplyKeyboardMarkup([
        [KeyboardButton("سوال"), KeyboardButton("سایر کتاب‌ها")]
    ], resize_keyboard=True)
    await update.message.reply_text(welcome_message, reply_markup=main_menu)

async def handle_message(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_message = update.message.text
    if user_message == "سوال":
        await ask_question(update, context)
    elif user_message == "سایر کتاب‌ها":
        await update.message.reply_text(
            "دوست عزیز برای دیدن سایر کتاب‌ها به ربات اصلی @pnu_azmon_bot مراجعه کنید. با تشکر ربات کوچک شما 🥰"
        )

async def ask_question(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user_data = context.user_data
    asked_questions = user_data.get("asked_questions", set())
    remaining_questions = [q for q in questions if q["id"] not in asked_questions]
    if not remaining_questions:
        await update.message.reply_text("تمام سوالات برای شما ارسال شده است. دوباره تلاش کنید!")
        return
    question_data = random.choice(remaining_questions)
    await send_poll_with_retry(
        context=context,
        chat_id=update.effective_chat.id,
        question=question_data["question"],
        options=question_data["options"],
        correct_option_id=question_data["correct_option_id"]
    )
    asked_questions.add(question_data["id"])
    user_data["asked_questions"] = asked_questions
    next_question_button = InlineKeyboardMarkup([
        [InlineKeyboardButton("سوال بعدی", callback_data="next_question")]
    ])
    await context.bot.send_message(chat_id=update.effective_chat.id, text="سوال بعدی را بزنید", reply_markup=next_question_button)

async def next_question(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    await query.answer()
    new_update = Update(update.update_id, message=query.message)
    await ask_question(new_update, context)

def main():
    app = ApplicationBuilder().token(TOKEN).build()
    app.add_handler(CommandHandler("start", start))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, handle_message))
    app.add_handler(CallbackQueryHandler(next_question, pattern="next_question"))
    app.run_polling()

if __name__ == "__main__":
    main()
