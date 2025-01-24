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



    

from aiogram import Bot, Dispatcher, types
from aiogram.contrib.middlewares.logging import LoggingMiddleware
from aiogram.utils import executor


bot = Bot(token=API_TOKEN)
dp = Dispatcher(bot)
dp.middleware.setup(LoggingMiddleware())

@dp.message_handler(commands=['start'])
async def send_welcome(message: types.Message):
    await bot.send_message(message.chat.id, " Отправьте мне ссылку на видео из VK/Rutube.")

@dp.message_handler(content_types=types.ContentType.link)
async def handle_document(message: types.Message):
    print(message)
    document_id = message.text._id
    file_info = await bot.get_file(text_id)
    await bot.download_file(file_info.file_path, f"./downloads/{message.text._name}")
    await bot.send_message(message.chat.id, f"Файл {message.text._name} получен и сохранен.")

@dp.message_handler(lambda message: message.text.lower() == "")
async def greet_user(message: types.Message):
    await message.reply("")

@dp.message_handler()
async def echo(message: types.Message):
    print(message)
    user_message = message.text.lower()
    if '' in user_message:
        await bot.send_message(message.chat.id, 'фильм "Пока не сыграл в ящик"')
    elif '' in user_message:
        await bot.send_message.chat.id, ('https://rutube.ru/video/539c76a8e8c8bf3168e73a9062e8e340/')
    else:
        await bot.send_message(message.chat.id, 'https://vkvideo.ru/video-76914461_456250842')

if __name__ == '__main__':
    executor.start_polling(dp, skip_updates=True)

