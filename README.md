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


def handle_quality_selection(update: Update, context: CallbackContext) -> None:
    query = update.callback_query
    query.answer()
    quality = query.data
    video_info = context.user_data.get('video_info')

    if video_info:
        query.edit_message_caption(caption=f"{video_info['platform']}\n{video_info['title']}\n{video_info['channel']}\nКачество: {quality}p\nЗагрузка...")
        video_url = video_info['video_urls'].get(quality)
        if video_url:
            query.message.reply_video(video=video_url, caption=f"{video_info['platform']}\n{video_info['title']}\n{video_info['channel']}\nКачество: {quality}p\nВаше видео загружено")
        else:
            query.edit_message_caption(caption=f"{video_info['platform']}\n{video_info['title']}\n{video_info['channel']}\nКачество: {quality}p\nОшибка загрузки видео.")
    else:
        query.edit_message_caption(caption="Ошибка: информация о видео не найдена.")


def get_rutube_video_info(url: str) -> dict:
    try:
        response = requests.get(url)
        soup = BeautifulSoup(response.text, 'html.parser')
        title = soup.find('meta', property='og:title')['content']
        thumbnail = soup.find('meta', property='og:image')['content']
        channel = soup.find('meta', property='og:site_name')['content']
        video_urls = {
            '1080': soup.find('link', {'type': 'video/mp4', 'data-quality': '1080'})['href'],
            '720': soup.find('link', {'type': 'video/mp4', 'data-quality': '720'})['href'],
            '480': soup.find('link', {'type': 'video/mp4', 'data-quality': '480'})['href'],
            '360': soup.find('link', {'type': 'video/mp4', 'data-quality': '360'})['href'],
            '240': soup.find('link', {'type': 'video/mp4', 'data-quality': '240'})['href'],
            '144': soup.find('link', {'type': 'video/mp4', 'data-quality': '144'})['href'],
        }
        return {
            'platform': 'Rutube',
            'title': title,
            'channel': channel,
            'thumbnail': thumbnail,
            'video_urls': video_urls
        }
    except Exception as e:
        logger.error(f"Error getting Rutube video info: {e}")
        return None


def get_vk_video_info(url: str) -> dict:
    try:
   
        return {
            'platform': 'VK',
            'title': 'Пример видео',
            'channel': 'Пример канала',
            'thumbnail': 'https://example.com/thumbnail.jpg',
            'video_urls': {
                '1080': 'https://example.com/video_1080.mp4',
                '720': 'https://example.com/video_720.mp4',
                '480': 'https://example.com/video_480.mp4',
                '360': 'https://example.com/video_360.mp4',
                '240': 'https://example.com/video_240.mp4',
                '144': 'https://example.com/video_144.mp4',
            }
        }
    except Exception as e:
        logger.error(f"Error getting VK video info: {e}")
        return None


def main() -> None:
    # Вставьте сюда ваш токен
    updater = Updater("YOUR_TELEGRAM_BOT_TOKEN")

    dispatcher = updater.dispatcher

    dispatcher.add_handler(CommandHandler("start", start))
    dispatcher.add_handler(MessageHandler(Filters.text & ~Filters.command, handle_video_link))
    dispatcher.add_handler(CallbackQueryHandler(handle_quality_selection))

    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()

