Telegram kanallariga tasodifiy tanaffus bilan qo'shilish va uchta asosiy holatni (muvaffaqiyat, allaqachon a'zo, xatolik) alohida ushlash uchun quyidagi kod ishlatiladi:

Python
import os
import sys
import random
import asyncio
from dotenv import load_dotenv
from telethon import TelegramClient
from telethon.tl.functions.channels import JoinChannelRequest
from telethon.errors import (
    UserAlreadyParticipantError,
    FloodWaitError,
    RPCError
)

load_dotenv()

API_ID = os.getenv("API_ID")
API_HASH = os.getenv("API_HASH")

if not API_ID or not API_HASH:
    print("Xatolik: API_ID yoki API_HASH .env faylida topilmadi!")
    sys.exit(1)

TARGET_CHANNELS = ["telegram", "durov", "python", "xato_kanal_test_123"]

client = TelegramClient("joiner_session", int(API_ID), API_HASH)

async def main():
    async with client:
        for channel in TARGET_CHANNELS:
            print(f"'{channel}' kanaliga ulanish so'rovi yuborilmoqda...")
            
            try:
                await client(JoinChannelRequest(channel))
                # 1. Muvaffaqiyatli qo'shilish
                print(f" -> [MUVAFFAQIYAT]: '{channel}' kanaliga muvaffaqiyatli qo'shildingiz.")

            except UserAlreadyParticipantError:
                # 2. Allaqachon a'zo bo'lingan holat
                print(f" -> [ALLAQACHON A'ZO]: Siz '{channel}' kanalining allaqachon a'zosisiz.")

            except (RPCError, FloodWaitError, Exception) as e:
                # 3. Xatolik yuz bergan holat
                print(f" -> [XATOLIK]: '{channel}' kanaliga qo'shilishda xatolik: {e}")

            # Qo'shilishlar orasida tasodifiy tanaffus (4 dan 10 soniyagacha)
            delay = random.uniform(4.0, 10.0)
            print(f"   ({delay:.1f} soniya kutilmoqda...)\n")
            await asyncio.sleep(delay)

if __name__ == "__main__":
    asyncio.run(main())
Asosiy qismlar:

Muvaffaqiyat: JoinChannelRequest xatosiz bajarilganda [MUVAFFAQIYAT] xabari chiqadi.

Allaqachon a'zo: UserAlreadyParticipantError istisnosi alohida ushlanadi.

Xatolik: Boshqa barcha texnik yoki tarmoq xatolari (RPCError, FloodWaitError va h.k.) umumiy xatolik sifatida ushlanadi.

Tasodifiy tanaffus: random.uniform(4.0, 10.0) funksiyasi orqali har bir so'rov orasida tasodifiy vaqt oralig'i (soniyalarda) kutiladi.
