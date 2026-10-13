import asyncio
from telethon import TelegramClient, events, Button
from telethon.sessions import StringSession

API_ID = 586010
API_HASH = 'd0dc2c4578cc199671651a4d0b794bd2'

# Sizning Termux'da olgan sessiya kalitingiz
SESSION_STRING = '1ApWapzMBu8I1h6edhv0QCQ_KA_sJ8uu6O10YanbLG9s0gQhwoLNBPZIIASbB410CszSjWY6aajDiV3j-cbaDCry4-Bxrm2Mdgqje_AoiiZvK80_M-NtJcDLVCzq5bzTKm_QWXrkccpH8lZwGjBj53pqKMH65fxV5VZzOHtC-AG1820mNXu6AMcpRc6fwxqvQ1Bexz-03CKH0OuqujPaEZqCKMLuKM7eVf94004EeCfZaYt9vjHslrvCXLP56s40KkOlPZ6rPHxjKTZrb2hLMzllQbne0OvI5wv8ejOSMcMhpEh4vECqhTrnI='

SOURCE_CHANNEL = 'vodiy_musics'
TARGET_CHANNEL = 'vulqonN1'

client = TelegramClient(StringSession(SESSION_STRING), API_ID, API_HASH)
pending_messages = {}

def modify_text(text):
    if not text:
        text = ""
    return f"{text}\n\n🎧 **Bizning kanal:** @vulqonN1"

@client.on(events.NewMessage(chats=SOURCE_CHANNEL))
async def handle_new_post(event):
    original_text = event.message.text or event.message.caption or ''
    modified_text = modify_text(original_text)
    msg_id = event.message.id
    
    media = None
    if event.message.media:
        media = await event.message.download_media()

    pending_messages[msg_id] = {
        'text': modified_text,
        'media': media
    }
    
    buttons = [
        [
            Button.inline("✅ Ha, to'g'ri", data=f"approve_{msg_id}".encode()),
            Button.inline("🔄 Qaytadan ishla", data=f"reprocess_{msg_id}".encode())
        ]
    ]
    
    caption_text = f"📌 **Yangi post (Tekshiruv uchun):**\n\n{modified_text}"
    
    if media:
        await client.send_file('me', media, caption=caption_text, buttons=buttons)
    else:
        await client.send_message('me', caption_text, buttons=buttons)

@client.on(events.CallbackQuery)
async def callback_handler(event):
    data = event.data.decode('utf-8')
    action, msg_id_str = data.split('_')
    msg_id = int(msg_id_str)
    
    if action == 'approve':
        if msg_id in pending_messages:
            msg_data = pending_messages[msg_id]
            if msg_data['media']:
                await client.send_file(TARGET_CHANNEL, msg_data['media'], caption=msg_data['text'])
            else:
                await client.send_message(TARGET_CHANNEL, msg_data['text'])
                
            await event.edit("✅ Post kanalga chop etildi!", buttons=None)
            del pending_messages[msg_id]
        else:
            await event.answer("⚠️ Xabar topilmadi yoki eskirgan.", alert=True)
            
    elif action == 'reprocess':
        await event.answer("🔄 Post qayta ishlandi.", alert=True)

async def main():
    print("Agent bulut serverda ishga tushmoqda...")
    await client.start()
    print("Agent muvaffaqiyatli ishlayapti!")
    await client.run_until_disconnected()

if __name__ == '__main__':
    asyncio.run(main())
