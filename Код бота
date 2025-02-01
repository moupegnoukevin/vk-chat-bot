import os
import vk_api
from vk_api.bot_longpoll import VkBotLongPoll, VkBotEventType
from dotenv import load_dotenv

load_dotenv()

class VKBot:
    def __init__(self):
        self.token = os.getenv("VK_TOKEN")
        self.group_id = os.getenv("GROUP_ID")
        self.vk_session = vk_api.VkApi(token=self.token)
        self.longpoll = VkBotLongPoll(self.vk_session, self.group_id)
        self.vk = self.vk_session.get_api()
        self.greeted_users = set()

    def run(self):
        for event in self.longpoll.listen():
            if event.type == VkBotEventType.MESSAGE_NEW:
                self.handle_message(event.object.message)

    def handle_message(self, message):
        user_id = message['from_id']
        attachments = message.get('attachments', [])
        photos = [att for att in attachments if att['type'] == 'photo']

        # Отправка приветствия новым пользователям
        if user_id not in self.greeted_users:
            self.vk.messages.send(
                user_id=user_id,
                message="Привет! Добро пожаловать в наш чат-бот.",
                random_id=0
            )
            self.greeted_users.add(user_id)

        # Возврат изображений
        if photos:
            photo_attachments = []
            for photo in photos:
                photo_data = photo['photo']
                owner_id = photo_data['owner_id']
                photo_id = photo_data['id']
                access_key = photo_data.get('access_key', '')
                attachment = f'photo{owner_id}_{photo_id}_{access_key}' if access_key else f'photo{owner_id}_{photo_id}'
                photo_attachments.append(attachment)
            
            self.vk.messages.send(
                user_id=user_id,
                message="Ваше изображение:",
                attachment=','.join(photo_attachments),
                random_id=0
            )

if __name__ == "__main__":
    bot = VKBot()
    bot.run()
