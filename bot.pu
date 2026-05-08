import os
import logging
from telegram import Update, ReplyKeyboardMarkup, ReplyKeyboardRemove
from telegram.ext import Application, CommandHandler, MessageHandler, filters, ConversationHandler, ContextTypes

# Логирование
logging.basicConfig(format="%(asctime)s - %(name)s - %(levelname)s - %(message)s", level=logging.INFO)
logger = logging.getLogger(__name__)

# Токен из переменной окружения
BOT_TOKEN = os.environ.get("BOT_TOKEN")
# Твой Telegram ID (для пересылки заявок)
YOUR_TG_USERNAME = "@irinataipro"

# Состояния диалога
(
    QUESTION_1, QUESTION_2, QUESTION_3, QUESTION_4,
    QUESTION_5, QUESTION_6, QUESTION_7,
    CONTACT_NAME, CONTACT_SPHERE, CONTACT_LINK
) = range(10)

# Баллы за ответы
SCORES = {
    "А": 3,
    "Б": 2,
    "В": 1,
}

user_scores = {}
user_answers = {}

# --- СТАРТ ---
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE) -> int:
    user = update.effective_user
    user_data = context.user_data
    user_data["score"] = 0
    user_data["answers"] = {}

    # Если есть ФИ, используем, иначе берем first_name
    name = user.full_name if user.full_name else user.first_name

    text = (
        f"{name}, рада приветствовать! 🔥\n\n"
        "Я Ирина Тай, маркетинговый стратег.\n\n"
        "В своей работе я прежде всего опираюсь на цифры и знаю: окупаемость "
        "рекламы напрямую зависит от точности выбора аудитории.\n\n"
        "Без этого фундамента бюджет часто расходуется на случайные контакты, а "
        "не на реальные продажи. Чтобы этого избежать, важно сначала убедиться, "
        "что ваше предложение попадает точно в запрос клиента, и только после "
        "этого масштабироваться.\n\n"
        "Перед тем как обсуждать стратегию и сроки, предлагаю пройти короткий "
        "тест. *Он поможет оценить готовность вашего проекта к росту и "
        "покажет:*\n\n"
        "▪️Насколько глубоко проработан портрет вашего клиента.\n"
        "▪️Существует ли риск нецелевого расхода бюджета.\n"
        "▪️С чего эффективнее начать: с диагностики аудитории или сразу с "
        "настройки трафика.\n\n"
        "*Это займет не больше 5 минут*. В конце вы получите экспертную оценку"
        "текущей ситуации и мои рекомендации 👇"
    )
    keyboard = [["Начать тест"]]
    reply_markup = ReplyKeyboardMarkup(keyboard, one_time_keyboard=True, resize_keyboard=True)
    await update.message.reply_text(text, parse_mode="Markdown", reply_markup=reply_markup)
    return QUESTION_1

# --- ВОПРОСЫ ---
async def ask_question(update, context, num, question_text, answers, next_state):
    keyboard = [[f"Ответ А"], [f"Ответ Б"], [f"Ответ В"]]
    reply_markup = ReplyKeyboardMarkup(keyboard, one_time_keyboard=True, resize_keyboard=True)
    await update.message.reply_text(question_text, parse_mode="Markdown", reply_markup=reply_markup)
    return next_state

async def question_1(update, context):
    return await ask_question(update, context, 1,
        "1️⃣ *Кто ваша целевая аудитория?*\n\nА) Я могу назвать 2–3 сегмента с понятными характеристиками (пол, возраст, доход, боли, ценности)\nБ) Знаю примерно, но размыто (например, «женщины 30–50 лет»)\nВ) Кажется, что «всем подходит», я не сегментировал клиентов",
        ["Ответ А", "Ответ Б", "Ответ В"], QUESTION_1)

async def question_2(update, context):
    return await ask_question(update, context, 2,
        "2️⃣ *Насколько точно вы понимаете, кто ваш клиент?*\n\nА) Точно понимаю, кому продаю и какие клиенты мне подходят.\nБ) Есть общее понимание, но не всегда понятно, почему приходят «не те» люди.\nВ) Боюсь представить. Я понимаю, что звонят и спрашивают часто не те люди, и время тратится впустую.",
        ["Ответ А", "Ответ Б", "Ответ В"], QUESTION_2)

async def question_3(update, context):
    return await ask_question(update, context, 3,
        "3️⃣ *Почему клиенты выбирают именно вас?*\n\nА) Знаю чётко: у меня есть 2–3 УТП, которые отличают меня от конкурентов, подтверждённые словами клиентов.\nБ) Думаю, что у нас хорошее качество/сервис/цена, но не записывал точные формулировки от клиентов.\nВ) Честно говоря, не знаю — я не спрашивал и не собирал обратную связь.",
        ["Ответ А", "Ответ Б", "Ответ В"], QUESTION_3)

async def question_4(update, context):
    return await ask_question(update, context, 4,
        "4️⃣ *Что мешает вашим клиентам купить?*\n\nА) Я знаю их ключевые страхи, барьеры и возражения (например, «дорого», «не верю в результат», «боюсь ошибиться»), я записываю это с реальных разговоров.\nБ) Догадываюсь, но не уверен — не систематизировал.\nВ) Никогда глубоко не анализировал возражения, работаю с теми, кто приходит.",
        ["Ответ А", "Ответ Б", "Ответ В"], QUESTION_4)

async def question_5(update, context):
    return await ask_question(update, context, 5,
        "5️⃣ *Насколько хорошо вы знаете язык своих клиентов?*\n\nА) Я использую те же слова и выражения, что и они. Я слышал их в разговорах, переписках, вопросах. Иногда это совсем не те формулировки, которыми я сам привык описывать свою работу.\nБ) Я говорю о продукте так, как принято в моей сфере. Думаю, клиенты понимают профессиональные термины и описания.\nВ) Я не сверял. Пишу так, как считаю правильным.",
        ["Ответ А", "Ответ Б", "Ответ В"], QUESTION_5)

async def question_6(update, context):
    return await ask_question(update, context, 6,
        "6️⃣ *Насколько вы уверены, что ваше предложение уникально для клиента?*\n\nА) Я могу сформулировать, чем я отличаюсь, словами клиента. Не «индивидуальный подход», а конкретная деталь, которую замечают и ценят те, кто ко мне приходит.\nБ) Я выделяюсь качеством и сервисом, но пока сложно показать это так, чтобы клиент сразу увидел разницу между мной и конкурентами.\nВ) Пока сложно сформулировать отличие. Кажется, в нашей нише у всех похожие предложения, и клиенту сложно увидеть разницу.",
        ["Ответ А", "Ответ Б", "Ответ В"], QUESTION_6)

async def question_7(update, context):
    return await ask_question(update, context, 7,
        "7️⃣ *Что происходит с клиентом после того, как он оставил заявку?*\n\nА) Я понимаю, с какими ожиданиями он пришёл и что ему важно услышать в первые минуты общения. У меня есть выстроенный сценарий первого касания, который снимает его тревогу и двигает к решению.\nБ) Обычно я или менеджер быстро связываемся, уточняем запрос и договариваемся о встрече или продаже. Без жёсткой структуры, но оперативно.\nВ) Отвечаю по возможности. Иногда быстро, иногда с задержкой — отдельной системы первого касания пока нет.",
        ["Ответ А", "Ответ Б", "Ответ В"], QUESTION_7)

# --- ОБРАБОТКА ВСЕХ ОТВЕТОВ ---
async def handle_answer(update, context, q_num, next_handler, score_key, answer_text_key):
    user_data = context.user_data
    answer = update.message.text.strip()
    # Получаем букву ответа
    letter = answer[-1] if answer[-1] in ["А", "Б", "В"] else None
    if not letter:
        await update.message.reply_text("Пожалуйста, выберите ответ, используя кнопку.")
        return q_num  # возвращаем на тот же вопрос

    score = SCORES.get(letter, 0)
    user_data["score"] += score
    user_data["answers"][answer_text_key] = answer

    # Переходим к следующему вопросу
    return await next_handler(update, context)

async def handle_q1(update, context):
    return await handle_answer(update, context, QUESTION_1, question_2, "score", "q1")

async def handle_q2(update, context):
    return await handle_answer(update, context, QUESTION_2, question_3, "score", "q2")

async def handle_q3(update, context):
    return await handle_answer(update, context, QUESTION_3, question_4, "score", "q3")

async def handle_q4(update, context):
    return await handle_answer(update, context, QUESTION_4, question_5, "score", "q4")

async def handle_q5(update, context):
    return await handle_answer(update, context, QUESTION_5, question_6, "score", "q5")

async def handle_q6(update, context):
    return await handle_answer(update, context, QUESTION_6, question_7, "score", "q6")

async def handle_q7(update, context):
    user_data = context.user_data
    answer = update.message.text.strip()
    letter = answer[-1] if answer[-1] in ["А", "Б", "В"] else None
    if not letter:
        await update.message.reply_text("Пожалуйста, выберите ответ, используя кнопку.")
        return QUESTION_7
    score = SCORES.get(letter, 0)
    user_data["score"] += score
    user_data["answers"]["q7"] = answer

    total_score = user_data["score"]
    await update.message.reply_text(f"Спасибо за ответы! Подсчитываю результаты...", reply_markup=ReplyKeyboardRemove())

    # Отправляем результат пользователю
    if total_score >= 19:
        await show_green_zone(update)
    elif total_score >= 12:
        await show_yellow_zone(update)
    else:
        await show_red_zone(update)
    return CONTACT_NAME

# --- ЗОНЫ РЕЗУЛЬТАТОВ ---
async def show_green_zone(update):
    text = (
        "*Результат: «Зелёная зона» (19 баллов и более)*\n"
        "*Проработанность аудитории: высокая — 70–80%.*\n\n"
        "Похоже, вы действительно знаете своих клиентов лучше, чем 90% "
        "предпринимателей. Это значит, что запуск рекламы возможен и риски потери "
        "бюджета снижены.\n\n"
        "Но есть нюанс. Даже при хорошем знании аудитории может «хромать» "
        "упаковка: посадочная страница, смыслы, оффер. И тогда деньги всё равно "
        "могут уйти не туда.\n\n"
        "Поэтому торопиться с выводами рано — нужно посмотреть на проект "
        "целиком.\n\n"
        "*Приглашаю вас на бесплатный 30-минутный созвон*, где:\n\n"
        "— посмотрим, что у вас уже сделано;\n"
        "— оценим, где сейчас могут теряться заявки и рекламный бюджет;\n"
        "— определим, можно ли заходить в рекламу сразу или сначала нужно "
        "усилить посадочную страницу, оффер и смыслы."
    )
    keyboard = [["Записаться на созвон-консультацию →"]]
    reply_markup = ReplyKeyboardMarkup(keyboard, one_time_keyboard=True, resize_keyboard=True)
    await update.message.reply_text(text, parse_mode="Markdown", reply_markup=reply_markup)

async def show_yellow_zone(update):
    text = (
        "*Результат: «Жёлтая зона» (12–18 баллов)*\n"
        "*Проработанность аудитории: средняя — 40–60%.*\n\n"
        "Вы знаете клиентов в общих чертах, но деталей, на которых строится "
        "точная реклама, пока нет.\n\n"
        "Это опасная зона: вроде бы «понятно», а на деле посылы не цепляют, "
        "бюджет утекает, лиды приходят не те.\n\n"
        "Если запустить рекламу с таким уровнем проработки, по моей практике *до "
        "30–50% бюджета может уходить впустую* — на нецелевые показы, "
        "случайных людей и слабые связки между аудиторией, оффером и посадочной "
        "страницей.\n\n"
        "Мой опыт показывает: за средними ответами почти всегда скрываются "
        "серьёзные пробелы. Поверхностными правками их не закрыть — сначала "
        "нужно понять, где именно теряется клиент и почему предложение не "
        "попадает в его ситуацию.\n\n"
        "В такой точке бизнесу нужна не догадка, а понятная опора по клиенту: кто "
        "он, где его искать, что ему важно, что мешает покупке и какие смыслы "
        "должны вести его к заявке.\n\n"
        "Именно для этого нужна стратегическая диагностика ЦА — не как разовый "
        "документ «для рекламы», а как дорожная карта по вашему клиенту.\n\n"
        "Без такой карты в бизнесе часто появляется хаос: время уходит на "
        "нецелевые обращения, деньги тратятся на слабые действия, а подходящие "
        "клиенты могут проходить мимо — просто, потому что не видят в "
        "предложении точного попадания в свою ситуацию.\n\n"
        "Диагностика помогает навести порядок и использовать эту базу в продажах, "
        "на сайте, в соцсетях, в офлайн-встречах, в переписках, в контенте и уже "
        "потом — в рекламе.\n\n"
        "*Приглашаю вас на бесплатный 30-минутный созвон*, где я покажу, как "
        "работает стратегическая диагностика, что вы получите на выходе и нужна "
        "ли она вам сейчас или пока достаточно точечной доработки."
    )
    keyboard = [["Записаться на созвон-консультацию →"]]
    reply_markup = ReplyKeyboardMarkup(keyboard, one_time_keyboard=True, resize_keyboard=True)
    await update.message.reply_text(text, parse_mode="Markdown", reply_markup=reply_markup)

async def show_red_zone(update):
    text = (
        "*Результат: «Красная зона» (11 баллов и менее)*\n"
        "*Проработанность аудитории: низкая (25% и ниже).*\n\n"
        "Это значит, что запуск рекламы сейчас — это риск потерять до 70% "
        "бюджета на нецелевые показы.\n\n"
        "Вы пока не знаете, кому продаёте, почему покупают или не покупают, и что "
        "сказать в рекламе, чтобы это исправить. Нецелевые показы, случайные лиды "
        "и разочарование — вот что ждёт проект без фундамента.\n\n"
        "Я предлагаю начать со стратегической диагностики аудитории. Это глубокая "
        "работа на 2–3 недели: интервью, анализ, сбор реального портрета "
        "клиента, изучение рынка и конкурентов. После неё вы будете знать:\n\n"
        "· кому вы продаёте,\n"
        "· что им важно слышать,\n"
        "· как строить рекламу без слива бюджета.\n\n"
        "Реклама после диагностики работает в разы точнее и дешевле.\n\n"
        "Запишитесь на бесплатный 30-минутный созвон — объясню логику "
        "стратегической диагностики и какой результат вы получите. Вы узнаете "
        "реальный портрет своей аудитории, работающие сообщения, причины отказов "
        "и способы их закрыть.\n\n"
        "Эти данные работают шире рекламы: сайт, офлайн-встречи, переговоры. "
        "Диагностика переводит бизнес из режима догадок в режим точных данных. "
        "Это значит — вы закрываете невидимые дыры, через которые прямо сейчас "
        "утекают время, деньги и потенциальные клиенты."
    )
    keyboard = [["Записаться на 30-минутный созвон о диагностике →"]]
    reply_markup = ReplyKeyboardMarkup(keyboard, one_time_keyboard=True, resize_keyboard=True)
    await update.message.reply_text(text, parse_mode="Markdown", reply_markup=reply_markup)

# --- СБОР КОНТАКТОВ ---
async def contact_name(update, context):
    user = update.effective_user
    name = user.full_name if user.full_name else user.first_name
    text = (
        f"{name}, чтобы я могла предметно подготовиться к нашему "
        "созвону, напишите, пожалуйста:\n\n"
        "— *Ваше имя*\n"
        "— *Сфера бизнеса*\n"
        "— *Ссылка на ваш сайт или посадочную страницу*"
    )
    await update.message.reply_text(text, parse_mode="Markdown", reply_markup=ReplyKeyboardRemove())
    return CONTACT_SPHERE

async def contact_sphere(update, context):
    user_data = context.user_data
    user_data["contact_name"] = update.message.text.strip()
    await update.message.reply_text("Напишите, пожалуйста, вашу *сферу бизнеса*", parse_mode="Markdown")
    return CONTACT_LINK

async def contact_link(update, context):
    user_data = context.user_data
    user_data["contact_sphere"] = update.message.text.strip()
    await update.message.reply_text("Напишите, пожалуйста, *ссылку на ваш сайт или посадочную страницу*", parse_mode="Markdown")
    return 99  # Следующий шаг — финал

async def finish(update, context):
    user_data = context.user_data
    user_data["contact_link"] = update.message.text.strip()

    user = update.effective_user
    user_name = user.full_name if user.full_name else user.first_name
    username = f"@{user.username}" if user.username else "не указан"

    # Финальное сообщение пользователю
    text = (
        f"{user_name}, спасибо, что прошли тест!\n\n"
        "Я свяжусь с вами в ближайшее время, чтобы обсудить результат и показать, "
        "какой следующий шаг поможет проекту двигаться точнее: без лишних "
        "действий, догадок и потери бюджета.\n\n"
        "Если хотите, подпишитесь на мой Telegram-канал, где я разбираю ошибки "
        "в сложных нишах👇"
    )
    keyboard = [["Подписаться на канал"]]
    reply_markup = ReplyKeyboardMarkup(keyboard, one_time_keyboard=True, resize_keyboard=True)
    await update.message.reply_text(text, parse_mode="Markdown", reply_markup=reply_markup)

    # Кнопка с ссылкой на канал
    await update.message.reply_text(
        "👉 [Нажмите сюда, чтобы перейти в канал](https://t.me/target_irinatai)",
        parse_mode="Markdown",
        disable_web_page_preview=True
    )

    # Отправляем заявку тебе в личку
    total_score = user_data.get("score", 0)
    if total_score >= 19:
        zone = "Зелёная"
    elif total_score >= 12:
        zone = "Жёлтая"
    else:
        zone = "Красная"

    msg_to_you = (
        f"📥 *Новая заявка с теста!*\n\n"
        f"👤 Имя: {user_data.get('contact_name', '—')}\n"
        f"📌 Telegram: {username}\n"
        f"📂 Сфера: {user_data.get('contact_sphere', '—')}\n"
        f"🔗 Ссылка: {user_data.get('contact_link', '—')}\n"
        f"📊 Баллы: {total_score}\n"
        f"🎯 Зона: {zone}\n\n"
        f"*Ответы:*\n"
        f"Q1: {user_data.get('answers', {}).get('q1', '—')}\n"
        f"Q2: {user_data.get('answers', {}).get('q2', '—')}\n"
        f"Q3: {user_data.get('answers', {}).get('q3', '—')}\n"
        f"Q4: {user_data.get('answers', {}).get('q4', '—')}\n"
        f"Q5: {user_data.get('answers', {}).get('q5', '—')}\n"
        f"Q6: {user_data.get('answers', {}).get('q6', '—')}\n"
        f"Q7: {user_data.get('answers', {}).get('q7', '—')}"
    )
    await context.bot.send_message(
        chat_id=YOUR_TG_USERNAME,
        text=msg_to_you,
        parse_mode="Markdown"
    )

    return ConversationHandler.END

async def cancel(update: Update, context: ContextTypes.DEFAULT_TYPE) -> int:
    await update.message.reply_text("Тест прерван.", reply_markup=ReplyKeyboardRemove())
    return ConversationHandler.END

# --- ЗАПУСК ---
def main():
    app = Application.builder().token(BOT_TOKEN).build()

    conv_handler = ConversationHandler(
        entry_points=[CommandHandler("start", start)],
        states={
            QUESTION_1: [MessageHandler(filters.Regex("^(Ответ А|Ответ Б|Ответ В)$"), handle_q1)],
            QUESTION_2: [MessageHandler(filters.Regex("^(Ответ А|Ответ Б|Ответ В)$"), handle_q2)],
            QUESTION_3: [MessageHandler(filters.Regex("^(Ответ А|Ответ Б|Ответ В)$"), handle_q3)],
            QUESTION_4: [MessageHandler(filters.Regex("^(Ответ А|Ответ Б|Ответ В)$"), handle_q4)],
            QUESTION_5: [MessageHandler(filters.Regex("^(Ответ А|Ответ Б|Ответ В)$"), handle_q5)],
            QUESTION_6: [MessageHandler(filters.Regex("^(Ответ А|Ответ Б|Ответ В)$"), handle_q6)],
            QUESTION_7: [MessageHandler(filters.Regex("^(Ответ А|Ответ Б|Ответ В)$"), handle_q7)],
            CONTACT_NAME: [
                MessageHandler(filters.Regex(".*созвон.*"), contact_name),
                MessageHandler(filters.TEXT & ~filters.COMMAND, contact_name)
            ],
            CONTACT_SPHERE: [MessageHandler(filters.TEXT & ~filters.COMMAND, contact_sphere)],
            CONTACT_LINK: [MessageHandler(filters.TEXT & ~filters.COMMAND, contact_link)],
            99: [MessageHandler(filters.TEXT & ~filters.COMMAND, finish)],
        },
        fallbacks=[CommandHandler("cancel", cancel)],
    )

    app.add_handler(conv_handler)
    logger.info("Бот запущен и готов к работе!")
    app.run_polling()

if __name__ == "__main__":
    main()
