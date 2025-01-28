https://github.com/slobodchikovlevisp229/Bot-in-telegram/edit/main/README.md
 # Bot-in-telegram
Бот в телеграмме, позволяющий скачивать видео, отправив ссылку на видео из ВК и Рутуб бесплатно, без рекламы, без просьб подписаться на другие телеграмм-каналы.
Открываете Telegram. В поисковой строке вбиваете запрос: Download Rutube&VK. Нажимаете на появившийся бот. Вошли в бот и нажимайте "Начать". Затем высветится окно: Добро пожаловать в Download Rutube&VK телеграмм-бот! Здесь вы сможете скачать видео из VK и Rutube, отправив ссылку на него бесплатно без регистрации и рекламы. Дальше высвечивается сообщение: Отправьте мне ссылку на видео из VK/Rutube. Пользователь отправляет ссылку на видео. Бот обрабатывает ссылку, отправляет обложку видео с кнопками внизу вариантов в каком формате скачать видео: 1080р, 720p, 480p, 360p, 240p, 144p (варианты качества изображения видео). Затем бот отправляет фото (обложку видео) с текстом внизу: Название видео, название канала, формат качества. 
Например:
Соц.сеть: Rutube
Видео: Пока не сыграл в ящик
Канал: Фильмач - кино и сериалы 
Качество изображения видео: 1080р 
Загрузка... 
Затем картинка меняется на видео и вместо надписи "Загрузка..." меняется на "ваше видео загружено", а текст выше с информацией о видео не меняется. 

import logging
import requests
from bs4 import BeautifulSoup
from telegram import Update, InlineKeyboardButton, InlineKeyboardMarkup
from telegram.ext import Updater, CommandHandler, MessageHandler, Filters, CallbackQueryHandler, CallbackContext
logging.basicConfig(format='%(asctime)s - %(name)s - %(levelname)s - %(message)s', level=logging.INFO)
logger = logging.getLogger(__name__)

start
def start(update: Update, context: CallbackContext) -> None:
    update.message.reply_text(
        "Добро пожаловать в Download Rutube&VK телеграмм-бот! "
        "Здесь вы сможете скачать видео из VK и Rutube, отправив ссылку на него бесплатно без регистрации и рекламы.\n\n"
        "Отправьте мне ссылку на видео из VK/Rutube."
    )


def handle_video_link(update: Update, context: CallbackContext) -> None:
    url = update.message.text
    if "rutube.ru" in url:
        video_info = get_rutube_video_info(url)
    elif "vk.com" in url:
        video_info = get_vk_video_info(url)
    else:
        update.message.reply_text("Пожалуйста, отправьте ссылку на видео из VK или Rutube.")
        return
          if video_info:
        keyboard = [
            [InlineKeyboardButton("1080p", callback_data='1080')],
            [InlineKeyboardButton("720p", callback_data='720')],
            [InlineKeyboardButton("480p", callback_data='480')],
            [InlineKeyboardButton("360p", callback_data='360')],
            [InlineKeyboardButton("240p", callback_data='240')],
            [InlineKeyboardButton("144p", callback_data='144')],
        ]
        reply_markup = InlineKeyboardMarkup(keyboard)
        update.message.reply_photo(
            photo=video_info['thumbnail'],
            caption=f"Соц.сеть: {video_info['platform']}\nВидео: {video_info['title']}\nКанал: {video_info['channel']}",
            reply_markup=reply_markup
        )
        context.user_data['video_info'] = video_info
    else:
        update.message.reply_text("Не удалось получить информацию о видео. Пожалуйста, проверьте ссылку.")


