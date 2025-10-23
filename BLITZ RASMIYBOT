# bot.py
import os
import json
import random
from datetime import time
import pytz
from typing import Dict, Any, List

from telegram import InlineKeyboardButton, InlineKeyboardMarkup, Update
from telegram.ext import (
    ApplicationBuilder,
    CommandHandler,
    CallbackQueryHandler,
    MessageHandler,
    ContextTypes,
    filters,
)

# ---------------------------
#  CONFIG
# ---------------------------
DATA_FILE = "user_data.json"
TIMEZONE = "Asia/Tashkent"
DAILY_WORDS_COUNT = 10
DAILY_SEND_HOURS = [9, 15, 20]   # 09:00, 15:00, 20:00 Tashkent reminders & daily words at 09:00 primarily
TOP10_SEND_HOUR = 20             # 20:00 Tashkent daily top10 broadcast

# ---------------------------
#  LESSONS (English & German A1-A2-B1-B2)
#  (You can expand lists as needed)
# ---------------------------
lessons = {
    "english": {
        "A1": [
            {"word": "Hello", "translation": "Salom"},
            {"word": "Good morning", "translation": "Xayrli tong"},
            {"word": "Good evening", "translation": "Xayrli kech"},
            {"word": "Good night", "translation": "Xayrli tun"},
            {"word": "How are you?", "translation": "Qandaysiz?"},
            {"word": "I am fine", "translation": "Men yaxshiman"},
            {"word": "Thank you", "translation": "Rahmat"},
            {"word": "Please", "translation": "Iltimos"},
            {"word": "Yes", "translation": "Ha"},
            {"word": "No", "translation": "Yo'q"},
            {"word": "Excuse me", "translation": "Kechirasiz"},
            {"word": "Sorry", "translation": "Uzr"},
            {"word": "What is your name?", "translation": "Ismingiz nima?"},
            {"word": "My name is ...", "translation": "Mening ismim ..."},
            {"word": "Nice to meet you", "translation": "Tanishganimdan xursandman"},
            {"word": "Goodbye", "translation": "Xayr"},
            {"word": "See you later", "translation": "Keyin ko‘rishamiz"},
            {"word": "Where are you from?", "translation": "Qayerdansiz?"},
            {"word": "I am from Uzbekistan", "translation": "Men O‘zbekistondanman"},
            {"word": "Do you speak English?", "translation": "Inglizcha gapirasizmi?"},
            {"word": "A little", "translation": "Biroz"},
            {"word": "I don't understand", "translation": "Tushunmayapman"},
            {"word": "Can you help me?", "translation": "Menga yordam bera olasizmi?"},
            {"word": "Where is the toilet?", "translation": "Hojatxona qayerda?"},
            {"word": "How much is it?", "translation": "Bu qancha turadi?"},
            {"word": "I like it", "translation": "Menga yoqadi"},
            {"word": "I don't like it", "translation": "Menga yoqmaydi"},
            {"word": "I am hungry", "translation": "Men ochman"},
            {"word": "I am thirsty", "translation": "Men chanqadim"},
            {"word": "I am tired", "translation": "Men charchadim"},
            {"word": "Today", "translation": "Bugun"},
            {"word": "Tomorrow", "translation": "Ertaga"},
            {"word": "Yesterday", "translation": "Kecha"},
            {"word": "Morning", "translation": "Ertalab"},
            {"word": "Afternoon", "translation": "Tushdan keyin"},
            {"word": "Evening", "translation": "Kechqurun"},
            {"word": "Night", "translation": "Tun"},
            {"word": "Friend", "translation": "Do‘st"},
            {"word": "Family", "translation": "Oila"},
            {"word": "Mother", "translation": "Ona"},
            {"word": "Father", "translation": "Ota"},
            {"word": "Brother", "translation": "Aka / Uka"},
            {"word": "Sister", "translation": "Opa / Singil"},
            {"word": "Home", "translation": "Uy"},
            {"word": "School", "translation": "Maktab"},
            {"word": "Work", "translation": "Ish"},
            {"word": "Book", "translation": "Kitob"},
            {"word": "Pen", "translation": "Ruchka"},
            {"word": "Table", "translation": "Stol"},
            {"word": "Chair", "translation": "Stul"},
            {"word": "Window", "translation": "Deraza"},
            {"word": "Door", "translation": "Eshik"},
            {"word": "Car", "translation": "Mashina"},
            {"word": "Bus", "translation": "Avtobus"},
            {"word": "Shop", "translation": "Do‘kon"},
            {"word": "Market", "translation": "Bozor"},
            {"word": "Money", "translation": "Pul"},
            {"word": "Food", "translation": "Oziq-ovqat"},
            {"word": "Water", "translation": "Suv"},
            {"word": "Tea", "translation": "Choy"},
            {"word": "Coffee", "translation": "Kofe"},
        ],
        "A2": [
            {"word": "I usually wake up at 7 o'clock", "translation": "Men odatda soat 7 da uyg‘onaman"},
            {"word": "I have breakfast", "translation": "Men nonushta qilaman"},
              {"word": "Where are you from?", "translation": "Qayerdansiz?"},
{"word": "I am from Uzbekistan.", "translation": "Men Oʻzbekistondanman."},
{"word": "What do you do?", "translation": "Siz nima ish qilasiz?"},
{"word": "I work in an office.", "translation": "Men ofisda ishlayman."},
{"word": "I am a student.", "translation": "Men talabayman."},
{"word": "Can you help me, please?", "translation": "Iltimos, menga yordam bera olasizmi?"},
{"word": "I don’t understand.", "translation": "Men tushunmayapman."},
{"word": "Can you speak slowly?", "translation": "Iltimos, sekinroq gapiring."},
{"word": "What time is it?", "translation": "Soat nechchi bo‘ldi?"},
{"word": "It’s half past seven.", "translation": "Soat yetti yarim."},
{"word": "I am going to the market.", "translation": "Men bozorga ketayapman."},
{"word": "How much does it cost?", "translation": "Bu qancha turadi?"},
{"word": "It costs five dollars.", "translation": "Bu besh dollar turadi."},
{"word": "I like watching movies.", "translation": "Men film tomosha qilishni yoqtiraman."},
{"word": "I don’t like getting up early.", "translation": "Men erta turishni yoqtirmayman."},
{"word": "The weather is nice today.", "translation": "Bugun ob-havo yoqimli."},
{"word": "It is raining outside.", "translation": "Tashqarida yomg‘ir yog‘ayapti."},
{"word": "I have two brothers and one sister.", "translation": "Mening ikki akam va bitta singlim bor."},
{"word": "I live in a small town.", "translation": "Men kichik shaharchada yashayman."},
{"word": "I am learning English.", "translation": "Men ingliz tilini o‘rganayapman."},
{"word": "I want to travel to Germany.", "translation": "Men Germaniyaga sayohat qilishni xohlayman."},
{"word": "Do you have any hobbies?", "translation": "Sizning hobbiyingiz bormi?"},
{"word": "I like playing football.", "translation": "Men futbol o‘ynashni yoqtiraman."},
{"word": "I usually get up at 7 o’clock.", "translation": "Men odatda soat 7 da turaman."},
{"word": "She goes to work by bus.", "translation": "U ishga avtobusda boradi."},
{"word": "I’m going to meet my friends.", "translation": "Men do‘stlarim bilan uchrashmoqchiman."},
{"word": "I’m looking for my keys.", "translation": "Men kalitlarimni qidirayapman."},
{"word": "Can I have a cup of coffee?", "translation": "Menga bitta kofe bersangiz bo‘ladimi?"},
{"word": "I’m sorry, I’m late.", "translation": "Kech qoldim, uzr."},
{"word": "I don’t know the answer.", "translation": "Men javobni bilmayman."},
{"word": "Let’s go for a walk.", "translation": "Yuring, sayrga chiqamiz."},
{"word": "What are you doing?", "translation": "Nima qilyapsiz?"},
{"word": "I’m reading a book.", "translation": "Men kitob o‘qiyapman."},
{"word": "It’s very cold today.", "translation": "Bugun juda sovuq."},
{"word": "Do you have time tomorrow?", "translation": "Ertaga vaqtingiz bormi?"},
{"word": "I’m not sure.", "translation": "Men ishonchim komil emas."},
{"word": "That’s a good idea!", "translation": "Bu ajoyib fikr!"},
{"word": "I feel tired today.", "translation": "Bugun o‘zimni charchagandek his qilyapman."},
{"word": "I have a headache.", "translation": "Mening boshim og‘riyapti."},
{"word": "Don’t worry.", "translation": "Xavotir olmang."},
{"word": "Be careful!", "translation": "Ehtiyot bo‘ling!"},
{"word": "I lost my phone.", "translation": "Telefonimni yo‘qotdim."},
{"word": "I will call you later.", "translation": "Sizga keyinroq qo‘ng‘iroq qilaman."},
{"word": "See you tomorrow!", "translation": "Ertaga ko‘rishamiz!"},
{"word": "Can I sit here?", "translation": "Men bu yerda o‘tirsam bo‘ladimi?"},
{"word": "I am very hungry.", "translation": "Men juda ochman."},
{"word": "I am thirsty.", "translation": "Men chanqadim."},
{"word": "I am looking for a hotel.", "translation": "Men mehmonxona qidirayapman."},
{"word": "Can you show me the way?", "translation": "Menga yo‘lni ko‘rsatib bera olasizmi?"},
{"word": "I am waiting for the bus.", "translation": "Men avtobusni kutayapman."},
{"word": "I’m afraid I can’t come.", "translation": "Afsus, men kela olmayman."},
{"word": "Don’t forget to call me.", "translation": "Menga qo‘ng‘iroq qilishni unutmang."},
{"word": "Everything is fine.", "translation": "Hammasi joyida."},
{"word": "I need some water.", "translation": "Menga biroz suv kerak."},
{"word": "It’s too expensive.", "translation": "Bu juda qimmat."},
{"word": "I will think about it.", "translation": "Bu haqda o‘ylab ko‘raman."},
{"word": "Let’s take a photo.", "translation": "Keling, suratga tushamiz."},
{"word": "Can you open the window?", "translation": "Derazani ochib bera olasizmi?"},
{"word": "Turn left at the corner.", "translation": "Burchakdan chapga buriling."},
{"word": "I’m at home now.", "translation": "Men hozir uyda man."},
{"word": "It’s time to go to bed.", "translation": "Uxlash vaqti bo‘ldi."},
            {"word": "I go to work by bus", "translation": "Men ishga avtobusda boraman"},
            {"word": "I come home at 6 pm", "translation": "Men soat 6 da uyga qaytaman"},
            {"word": "I like reading books", "translation": "Menga kitob o‘qish yoqadi"},
            {"word": "I like watching movies", "translation": "Menga film ko‘rish yoqadi"},
            {"word": "Can I have the menu, please?", "translation": "Iltimos, menyuni bera olasizmi?"},
            {"word": "I have a reservation", "translation": "Menda band joy bor"},
            {"word": "I would like a single room", "translation": "Menga bitta kishilik xona kerak"},
            {"word": "How much is one night?", "translation": "Bir kechasi qancha turadi?"},
            {"word": "I need a taxi", "translation": "Menga taksi kerak"},
            {"word": "How long does it take?", "translation": "Bu qancha vaqt oladi?"},
            {"word": "It takes about 20 minutes", "translation": "Bu taxminan 20 daqiqa oladi"},
            {"word": "Where can I buy tickets?", "translation": "Chipta qayerdan olish mumkin?"},
            {"word": "I want to make a complaint", "translation": "Men shikoyat qilmoqchiman"},
            {"word": "Could you repeat that?", "translation": "Shuni takrorlab bera olasizmi?"},
            {"word": "Please speak slowly", "translation": "Iltimos, sekinroq gapiring"},
            {"word": "I don't feel well", "translation": "O‘zimni yaxshi his qilmayapman"},
            {"word": "I would like to pay by card", "translation": "Men karta bilan to‘lamoqchiman"},
            {"word": "Where is the nearest hospital?", "translation": "Eng yaqin shifoxona qayerda?"},
            {"word": "I am looking for a hotel", "translation": "Men mehmonxona qidirmoqdaman"},
            {"word": "I have a reservation", "translation": "Menda rezervatsiya bor"},
            {"word": "Can you show me on the map?", "translation": "Xaritada ko‘rsata olasizmi?"},
            {"word": "I need directions", "translation": "Menga yo‘l ko‘rsatish kerak"},
            {"word": "Is breakfast included?", "translation": "Nonushta kiritilganmi?"},
        ],
        "B1": [
            {"word": "actually", "translation": "aslida"},
            {"word": "agree", "translation": "rozilik bildirmoq"},
            {"word": "almost", "translation": "deyarli"},
            {"word": "already", "translation": "allaqachon"},
            {"word": "arrive", "translation": "yetib kelmoq"},
            {"word": "believe", "translation": "ishonmoq"},
            {"word": "borrow", "translation": "qarz olmoq"},
            {"word": "bring", "translation": "olib kelmoq"},
            {"word": "build", "translation": "qurmoq"},
            {"word": "choose", "translation": "tanlamoq"},
            {"word": "decide", "translation": "qaror qilmoq"},
            {"word": "describe", "translation": "tasvirlamoq"},
            {"word": "enjoy", "translation": "rohatlanmoq"},
            {"word": "experience", "translation": "tajriba"},
            {"word": "explain", "translation": "tushuntirmoq"},
            {"word": "forget", "translation": "unutmoq"},
            {"word": "improve", "translation": "yaxshilamoq"},
            {"word": "information", "translation": "ma'lumot"},
            {"word": "invite", "translation": "taklif qilmoq"},
            {"word": "learn", "translation": "o'rganmoq"},
            {"word": "lose", "translation": "yo'qotmoq"},
            {"word": "pay attention", "translation": "e'tibor bermoq"},
            {"word": "plan", "translation": "reja tuzmoq"},
            {"word": "prefer", "translation": "afzal ko'rmoq"},
            {"word": "probably", "translation": "ehtimol"},
            {"word": "promise", "translation": "va'da bermoq"},
            {"word": "receive", "translation": "qabul qilmoq"},
            {"word": "remember", "translation": "eslab qolmoq"},
            {"word": "report", "translation": "hisobot/ma'lumot bermoq"},
            {"word": "result", "translation": "natija"},
            {"word": "share", "translation": "bo'lishmoq"},
            {"word": "solve", "translation": "hal qilmoq"},
            {"word": "stay", "translation": "qolmoq"},
            {"word": "still", "translation": "hali ham"},
            {"word": "success", "translation": "muvaffaqiyat"},
            {"word": "travel", "translation": "sayohat qilmoq"},
            {"word": "try", "translation": "urinmoq"},
            {"word": "understand", "translation": "tushunmoq"},
            {"word": "wonder", "translation": "hayron bo'lmoq"},
               {"word": "I’ve been learning English for two years.", "translation": "Men ikki yildan beri ingliz tilini o‘rganayapman."},
{"word": "I think this city is very beautiful.", "translation": "Menimcha, bu shahar juda chiroyli."},
{"word": "I’m interested in learning new languages.", "translation": "Men yangi tillarni o‘rganishga qiziqaman."},
{"word": "Could you tell me where the bank is?", "translation": "Iltimos, bank qayerda ekanligini ayta olasizmi?"},
{"word": "I’m planning to visit London next summer.", "translation": "Men kelasi yozda Londonga tashrif buyurmoqchiman."},
{"word": "I don’t mind waiting.", "translation": "Kutish men uchun muammo emas."},
{"word": "I’ve never been abroad before.", "translation": "Men ilgari hech qachon chet elga chiqmaganman."},
{"word": "I’m looking forward to seeing you.", "translation": "Sizni ko‘rishni intiqlik bilan kutayapman."},
{"word": "It depends on the weather.", "translation": "Bu ob-havoga bog‘liq."},
{"word": "I haven’t decided yet.", "translation": "Men hali qaror qilmaganman."},
{"word": "I’m not sure what to do.", "translation": "Nima qilishni bilmayapman."},
{"word": "I feel much better today.", "translation": "Bugun o‘zimni ancha yaxshi his qilyapman."},
{"word": "I was born in Tashkent.", "translation": "Men Toshkentda tug‘ilganman."},
{"word": "It’s not as easy as it looks.", "translation": "Bu ko‘ringandek oson emas."},
{"word": "I don’t have enough time.", "translation": "Menda yetarlicha vaqt yo‘q."},
{"word": "I usually spend my weekends with friends.", "translation": "Men odatda dam olish kunlarimni do‘stlarim bilan o‘tkazaman."},
{"word": "I’ve just finished my homework.", "translation": "Men endigina uy vazifamni tugatdim."},
{"word": "I’m trying to improve my English skills.", "translation": "Men ingliz tilimni yaxshilashga harakat qilyapman."},
{"word": "What do you think about this idea?", "translation": "Bu fikr haqida nima deb o‘ylaysiz?"},
{"word": "I agree with you.", "translation": "Men siz bilan roziman."},
{"word": "I don’t agree at all.", "translation": "Men umuman rozi emasman."},
{"word": "That’s not true.", "translation": "Bu to‘g‘ri emas."},
{"word": "It makes sense.", "translation": "Bu mantiqan to‘g‘ri."},
{"word": "I’ve got a lot of work to do.", "translation": "Menda juda ko‘p ishlar bor."},
{"word": "Let’s stay in touch.", "translation": "Aloqada bo‘lib turaylik."},
{"word": "I missed the bus.", "translation": "Men avtobusni o‘tkazib yubordim."},
{"word": "I’m used to getting up early.", "translation": "Men erta turishga o‘rganib qolganman."},
{"word": "I didn’t mean to hurt you.", "translation": "Sizni xafa qilmoqchi emasdim."},
{"word": "I’m just kidding.", "translation": "Men shunchaki hazillashyapman."},
{"word": "It’s my first time here.", "translation": "Bu yerga birinchi marta keldim."},
{"word": "I’m sorry for being late.", "translation": "Kech qolgani uchun uzr."},
{"word": "It’s too crowded here.", "translation": "Bu yer juda gavjum."},
{"word": "Can I pay by card?", "translation": "Karta orqali to‘lasam bo‘ladimi?"},
{"word": "I’m afraid I lost my wallet.", "translation": "Afsuski, hamyonimni yo‘qotdim."},
{"word": "It’s getting dark.", "translation": "Kech bo‘layapti."},
{"word": "The movie was really interesting.", "translation": "Film juda qiziqarli edi."},
{"word": "I’m thinking about changing my job.", "translation": "Men ish joyimni o‘zgartirish haqida o‘ylayapman."},
{"word": "That sounds great!", "translation": "Bu juda ajoyib eshitiladi!"},
{"word": "It’s not my fault.", "translation": "Bu mening aybim emas."},
{"word": "I’m proud of you.", "translation": "Men sizdan faxrlanaman."},
{"word": "I’m a bit nervous.", "translation": "Men biroz hayajondaman."},
{"word": "I’m not feeling well today.", "translation": "Bugun o‘zimni yaxshi his qilmayapman."},
{"word": "I’m sure everything will be fine.", "translation": "Ishonchim komil, hammasi yaxshi bo‘ladi."},
{"word": "I’ve just moved to a new apartment.", "translation": "Men yaqinda yangi kvartiraga ko‘chdim."},
{"word": "I’ll do my best.", "translation": "Qo‘limdan kelganicha harakat qilaman."},
{"word": "I’m going to take a shower.", "translation": "Men hozir dush qabul qilaman."},
{"word": "It’s not a big deal.", "translation": "Bu muhim narsa emas."},
{"word": "That’s exactly what I thought.", "translation": "Aynan shunday deb o‘ylagan edim."},
{"word": "I’m happy to hear that.", "translation": "Buni eshitganimdan xursandman."},
{"word": "It’s a bit expensive, isn’t it?", "translation": "Bu biroz qimmat, shunday emasmi?"},
{"word": "I’m too tired to go out.", "translation": "Tashqariga chiqishga juda charchadim."},
{"word": "I don’t feel like working today.", "translation": "Bugun ishlagim kelmayapti."},
{"word": "I was surprised by the news.", "translation": "Men bu yangilikdan hayron qoldim."},
{"word": "It’s a long way from here.", "translation": "Bu joy bu yerdan uzoq."},
{"word": "The food smells delicious.", "translation": "Taomning hidi juda yoqimli."},
{"word": "I’m not used to this weather.", "translation": "Men bu ob-havoga o‘rganmaganman."},
{"word": "I can’t believe it!", "translation": "Ishonolmayapman!"},
{"word": "I think something is wrong.", "translation": "Menimcha, nimadir noto‘g‘ri."},
{"word": "I’ll call you as soon as I arrive.", "translation": "Men yetib borgach, sizga darhol qo‘ng‘iroq qilaman."},
{"word": "I forgot to bring my umbrella.", "translation": "Soyabonimni olishni unutdim."},
{"word": "He doesn’t listen to me.", "translation": "U meni eshitmaydi."},
{"word": "We’ve run out of milk.", "translation": "Sut tugadi."},
{"word": "I’d like to order something to eat.", "translation": "Men ovqat buyurtma bermoqchiman."},
{"word": "It’s hard to explain.", "translation": "Tushuntirish qiyin."},
{"word": "I’m sorry to bother you.", "translation": "Sizni bezovta qilganim uchun uzr."},
{"word": "I’m sure you’ll like it.", "translation": "Ishonchim komil, sizga yoqadi."},
{"word": "I’ve just come back from work.", "translation": "Men endi ishdan qaytdim."},
{"word": "Let’s celebrate!", "translation": "Keling, nishonlaymiz!"},
{"word": "I’m on my way.", "translation": "Yo‘ldaman."},
{"word": "I’m in a hurry.", "translation": "Men shoshayapman."},
{"word": "That’s not what I meant.", "translation": "Men buni nazarda tutmagandim."},
{"word": "Let’s keep in touch.", "translation": "Aloqada bo‘laylik."},
{"word": "I can’t wait to see you.", "translation": "Sizni ko‘rishga sabrim yo‘q."},
{"word": "I’m working on a new project.", "translation": "Men yangi loyihada ishlayapman."},
{"word": "It’s getting late.", "translation": "Kech bo‘layapti."},
{"word": "I need to get some rest.", "translation": "Men biroz dam olishim kerak."},
{"word": "I’ve never seen anything like that.", "translation": "Men bunday narsani hech qachon ko‘rmaganman."},
{"word": "I’m not sure I understand.", "translation": "Men tushungan-tushunmaganligimga ishonchim komil emas."},
{"word": "I’m thinking of buying a new phone.", "translation": "Men yangi telefon sotib olishni o‘ylayapman."},
{"word": "I’ve got a meeting this afternoon.", "translation": "Bugun tushlikdan keyin yig‘ilishim bor."},
{"word": "It was nice talking to you.", "translation": "Siz bilan suhbatlashish yoqimli edi."},
{"word": "I’ll let you know.", "translation": "Sizga xabar beraman."},
{"word": "I’m not ready yet.", "translation": "Men hali tayyor emasman."},
{"word": "I’ll be back soon.", "translation": "Men tez orada qaytaman."},
{"word": "You should take a break.", "translation": "Siz dam olishingiz kerak."},
{"word": "That’s not fair.", "translation": "Bu adolatli emas."},
{"word": "I’m afraid I can’t help you.", "translation": "Afsuski, men sizga yordam bera olmayman."},
{"word": "I hope everything goes well.", "translation": "Umid qilamanki, hammasi yaxshi bo‘ladi."},
{"word": "It’s nice to see you again.", "translation": "Sizni yana ko‘rganimdan xursandman."},
            {"word": "wrong", "translation": "noto'g'ri"},
        ],
        "B2": [
            {"word": "improve communication", "translation": "muloqotni yaxshilash"},
            {"word": "expand vocabulary", "translation": "so'z boyligini kengaytirish"},
            {"word": "cultural understanding", "translation": "madaniy tushuncha"},
            {"word": "perspective", "translation": "qarash"},
            {"word": "debate", "translation": "munozara"},
            {"word": "evidence", "translation": "dalil"},
             {"word": "I’ve been trying to contact you all day.", "translation": "Men butun kun siz bilan bog‘lanishga harakat qilayapman."},
{"word": "I didn’t expect things to change so quickly.", "translation": "Men hammasi shunchalik tez o‘zgaradi deb o‘ylamagandim."},
{"word": "It’s been ages since we last met.", "translation": "Oxirgi marta ko‘rishganimizga ancha vaqt bo‘ldi."},
{"word": "I’m really looking forward to this weekend.", "translation": "Men bu hafta oxirini intiqlik bilan kutayapman."},
{"word": "If I had known, I would have acted differently.", "translation": "Bilganimda, boshqacha yo‘l tutgan bo‘lardim."},
{"word": "I’d rather stay at home tonight.", "translation": "Bugun kechqurun uyda qolganimni afzal ko‘raman."},
{"word": "It’s not worth the risk.", "translation": "Bu xavfga arzimas."},
{"word": "To be honest, I wasn’t impressed.", "translation": "Rostini aytsam, men taassurot qoldirmadi."},
{"word": "I’ve heard a lot about you.", "translation": "Men siz haqingizda ko‘p eshitganman."},
{"word": "I can’t make up my mind.", "translation": "Men qaror qabul qila olmayapman."},
{"word": "It’s up to you to decide.", "translation": "Qaror sizga bog‘liq."},
{"word": "I’m not sure I can handle it.", "translation": "Men bunga bardosh bera olamanmi, ishonchim komil emas."},
{"word": "Let’s get straight to the point.", "translation": "Keling, bevosita mavzuga o‘taylik."},
{"word": "I couldn’t agree more.", "translation": "Men to‘liq qo‘shilaman."},
{"word": "That’s easier said than done.", "translation": "Aytilgani oson, ammo bajarish qiyin."},
{"word": "I can’t stand waiting in long lines.", "translation": "Uzun navbatlarda kutishga chiday olmayman."},
{"word": "I’d better finish this before it’s too late.", "translation": "Kech bo‘lmasdan oldin buni tugatganim yaxshi."},
{"word": "It reminds me of my childhood.", "translation": "Bu menga bolaligimni eslatadi."},
{"word": "As far as I know, he’s already left.", "translation": "Bilishimcha, u allaqachon ketgan."},
{"word": "I’m not used to working under pressure.", "translation": "Men bosim ostida ishlashga o‘rganmaganman."},
{"word": "It’s about time we did something.", "translation": "Endi nimanidir qilish vaqti keldi."},
{"word": "You should take it into consideration.", "translation": "Buni e’tiborga olishingiz kerak."},
{"word": "I can’t afford to make mistakes.", "translation": "Men xato qilishga yo‘l qo‘ya olmayman."},
{"word": "I’m aware of the situation.", "translation": "Men vaziyatdan xabardorman."},
{"word": "We need to come up with a better plan.", "translation": "Biz yaxshiroq reja topishimiz kerak."},
{"word": "He’s always complaining about something.", "translation": "U har doim nimadandir shikoyat qiladi."},
{"word": "It’s definitely worth visiting.", "translation": "Bu joyga borishga arziydi."},
{"word": "I’m considering moving abroad.", "translation": "Men chet elga ko‘chishni o‘ylayapman."},
{"word": "Let’s focus on finding a solution.", "translation": "Keling, yechim topishga e’tibor qarataylik."},
{"word": "I wasn’t aware of that.", "translation": "Men buni bilmagan edim."},
{"word": "I’m tired of doing the same thing every day.", "translation": "Har kuni bir xil ishni qilishdan charchadim."},
{"word": "It was a waste of time.", "translation": "Bu vaqtni bekorga sarflash edi."},
{"word": "I can’t help laughing when he talks.", "translation": "U gapirganda kulmasdan turolmayman."},
{"word": "I’m not in the mood for going out.", "translation": "Tashqariga chiqish kayfiyatida emasman."},
{"word": "I’m not sure if I can trust him.", "translation": "Men unga ishonish mumkinligiga ishonchim komil emas."},
{"word": "It took me a while to get used to it.", "translation": "Bunga o‘rganishimga biroz vaqt ketdi."},
{"word": "I’m having trouble understanding this topic.", "translation": "Men bu mavzuni tushunishda qiynalayapman."},
{"word": "I’m not convinced that it’s true.", "translation": "Buning rostligiga ishonganim yo‘q."},
{"word": "That’s exactly what I was thinking.", "translation": "Men ham aynan shunday o‘ylagan edim."},
{"word": "It doesn’t make any difference to me.", "translation": "Bu men uchun farq qilmaydi."},
{"word": "I’m looking forward to hearing from you soon.", "translation": "Sizdan tez orada xabar kutayapman."},
{"word": "I’m not sure what’s going on.", "translation": "Nima bo‘layotganini bilmayapman."},
{"word": "I wasn’t expecting that.", "translation": "Men buni kutmagandim."},
{"word": "I’m beginning to understand how it works.", "translation": "Bu qanday ishlashini tushuna boshlayapman."},
{"word": "He’s known for being very patient.", "translation": "U sabrli bo‘lishi bilan tanilgan."},
{"word": "It’s supposed to be easy, but it’s not.", "translation": "Bu oson bo‘lishi kerak edi, lekin unday emas."},
{"word": "I didn’t mean to interrupt you.", "translation": "Sizni bo‘lish niyatim yo‘q edi."},
{"word": "I guess you’re right.", "translation": "Menimcha, siz haqingiz."},
{"word": "It’s hard to make everyone happy.", "translation": "Hamma odamni baxtli qilish qiyin."},
{"word": "I’ll think about it and let you know.", "translation": "Men bu haqida o‘ylab, sizga aytaman."},
{"word": "I wasn’t in the mood for talking.", "translation": "Men gaplashish kayfiyatida emasdim."},
{"word": "He ended up buying a new one.", "translation": "U oxir-oqibat yangisini sotib oldi."},
{"word": "I was wondering if you could help me.", "translation": "Siz menga yordam bera olasizmi, deb o‘ylayapman."},
{"word": "It’s easier than I expected.", "translation": "Bu kutilganidan osonroq."},
{"word": "I can’t get used to waking up early.", "translation": "Men erta turishga o‘rgana olmayapman."},
{"word": "It’s a matter of time.", "translation": "Bu faqat vaqt masalasi."},
{"word": "He seems to be in a bad mood.", "translation": "U yomon kayfiyatda ko‘rinadi."},
{"word": "I didn’t realize it was so late.", "translation": "Bunday kech bo‘lganini payqamadim."},
{"word": "I wasn’t aware of the problem.", "translation": "Men muammodan xabardor emasdim."},
{"word": "Let’s call it a day.", "translation": "Buguncha yetarli, to‘xtaymiz."},
{"word": "It was nothing serious.", "translation": "Bu jiddiy narsa emas edi."},
{"word": "I’ve got mixed feelings about it.", "translation": "Bu haqda ikkilanayotganman."},
{"word": "It didn’t turn out as planned.", "translation": "Rejalashtirilganidek bo‘lmadi."},
{"word": "I wish I had more free time.", "translation": "Ko‘proq bo‘sh vaqtim bo‘lishini istardim."},
{"word": "I couldn’t help but laugh.", "translation": "Kulmasdan turolmadim."},
{"word": "It’s getting harder and harder to focus.", "translation": "E’tibor qaratish tobora qiyinlashmoqda."},
{"word": "I had no idea what to say.", "translation": "Nima deyishni bilmasdim."},
{"word": "It’s not as simple as it seems.", "translation": "Bu ko‘ringandek oddiy emas."},
{"word": "I can’t imagine living anywhere else.", "translation": "Boshqa joyda yashashni tasavvur qila olmayman."},
{"word": "It’s completely up to you.", "translation": "Bu to‘liq sizga bog‘liq."},
{"word": "I might have made a mistake.", "translation": "Men xato qilgan bo‘lishim mumkin."},
{"word": "I’ll get back to you as soon as I can.", "translation": "Imkonim boricha tez siz bilan bog‘lanaman."},
{"word": "I’d appreciate your help.", "translation": "Yordamingizni qadrlayman."},
{"word": "He tends to be late for meetings.", "translation": "U odatda yig‘ilishlarga kechikadi."},
{"word": "I’d rather not talk about it.", "translation": "Bu haqda gapirmaganimni afzal ko‘raman."},
{"word": "It didn’t go according to plan.", "translation": "Bu reja bo‘yicha ketmadi."},
{"word": "I’m used to dealing with stress.", "translation": "Men stress bilan kurashishga o‘rganib qolganman."},
{"word": "It’s not worth arguing about.", "translation": "Bu haqida bahslashishga arzimaydi."},
{"word": "It doesn’t make any sense.", "translation": "Bu hech qanday ma’no bermaydi."},
{"word": "It was love at first sight.", "translation": "Bu birinchi ko‘rishda sevgi edi."},
{"word": "I’d like to point out something important.", "translation": "Men muhim narsani ta’kidlashni istayman."},
{"word": "I had trouble sleeping last night.", "translation": "Kecha uxlashda qiynaldim."},
{"word": "It seems we’re on the same page.", "translation": "Ko‘rinishidan, biz bir fikrdamiz."},
{"word": "I’ll take your advice.", "translation": "Sizning maslahatingizni qabul qilaman."},
{"word": "It’s not what I expected.", "translation": "Bu men kutgan narsa emas."},
{"word": "I’m not really sure how to say it.", "translation": "Buni qanday aytishni bilmayapman."},
{"word": "I’m confident we’ll succeed.", "translation": "Ishonchim komil, biz muvaffaqiyat qozonamiz."},
{"word": "I had to make a tough decision.", "translation": "Men og‘ir qaror qabul qilishimga to‘g‘ri keldi."},
{"word": "I’m starting to feel tired.", "translation": "Men charchashni his qila boshladim."},
{"word": "It’s time to move on.", "translation": "Oldinga yurish vaqti keldi."},
{"word": "I’m sure we can figure it out.", "translation": "Ishonchim komil, biz buni hal qila olamiz."},
{"word": "Let’s keep it simple.", "translation": "Keling, soddaroq qilaylik."},
{"word": "It’s hard to believe, but it’s true.", "translation": "Ishonish qiyin, lekin bu rost."},
{"word": "I’ve learned a lot from this experience.", "translation": "Bu tajribadan ko‘p narsa o‘rgandim."},
{"word": "I’ll do whatever it takes.", "translation": "Nima bo‘lsa ham, buni qilaman."},
{"word": "I’m not sure if it’s a good idea.", "translation": "Bu yaxshi fikrmi, ishonchim komil emas."},
{"word": "It’s not what it looks like.", "translation": "Bu ko‘ringandek emas."},
{"word": "Let’s not rush into anything.", "translation": "Hech narsaga shoshilmaylik."},
            {"word": "complex", "translation": "murakkab"},
            {"word": "significant", "translation": "muhim"},
        ],
    },
    "german": {
        "A1": [
            {"word": "Woher kommst du?", "translation": "Qayerdansan?"},
             {"word": "Hallo", "translation": "Salom"},
{"word": "Guten Morgen", "translation": "Xayrli tong"},
{"word": "Guten Tag", "translation": "Xayrli kun"},
{"word": "Guten Abend", "translation": "Xayrli kech"},
{"word": "Gute Nacht", "translation": "Xayrli tun"},
{"word": "Tschüss", "translation": "Xayr"},
{"word": "Ja", "translation": "Ha"},
{"word": "Nein", "translation": "Yo‘q"},
{"word": "Bitte", "translation": "Iltimos / Marhamat"},
{"word": "Danke", "translation": "Rahmat"},
{"word": "Vielen Dank", "translation": "Katta rahmat"},
{"word": "Entschuldigung", "translation": "Kechirasiz"},
{"word": "Wie geht’s?", "translation": "Qandaysiz?"},
{"word": "Mir geht’s gut", "translation": "Men yaxshiman"},
{"word": "Und dir?", "translation": "Senga-chi?"},
{"word": "Ich heiße Ali", "translation": "Mening ismim Ali"},
{"word": "Ich komme aus Usbekistan", "translation": "Men O‘zbekistondanman"},
{"word": "Ich bin 20 Jahre alt", "translation": "Men 20 yoshdaman"},
{"word": "Wo wohnst du?", "translation": "Qayerda yashaysan?"},
{"word": "Ich wohne in Taschkent", "translation": "Men Toshkentda yashayman"},
{"word": "Was machst du?", "translation": "Nima qilyapsan?"},
{"word": "Ich lerne Deutsch", "translation": "Men nemis tilini o‘rganayapman"},
{"word": "Ich arbeite", "translation": "Men ishlayman"},
{"word": "Ich studiere", "translation": "Men o‘qiyman"},
{"word": "Ich habe Hunger", "translation": "Men ochman"},
{"word": "Ich habe Durst", "translation": "Men chanqadim"},
{"word": "Ich verstehe", "translation": "Men tushunaman"},
{"word": "Ich verstehe nicht", "translation": "Men tushunmayman"},
{"word": "Können Sie das wiederholen?", "translation": "Iltimos, qayta ayta olasizmi?"},
{"word": "Langsamer bitte", "translation": "Sekinroq, iltimos"},
{"word": "Wo ist das?", "translation": "Bu qayerda?"},
{"word": "Wie viel kostet das?", "translation": "Bu qancha turadi?"},
{"word": "Ich möchte Wasser", "translation": "Men suv xohlayman"},
{"word": "Ich nehme das", "translation": "Men buni olaman"},
{"word": "Ich liebe dich", "translation": "Men seni sevaman"},
{"word": "Ich bin müde", "translation": "Men charchadim"},
{"word": "Ich bin krank", "translation": "Men kasalman"},
{"word": "Mir ist kalt", "translation": "Menga sovuq"},
{"word": "Mir ist warm", "translation": "Menga issiq"},
{"word": "Was ist das?", "translation": "Bu nima?"},
{"word": "Ich weiß nicht", "translation": "Men bilmayman"},
{"word": "Ich brauche Hilfe", "translation": "Menga yordam kerak"},
{"word": "Kein Problem", "translation": "Muammo yo‘q"},
{"word": "Alles klar", "translation": "Hammasi joyida"},
{"word": "Ich bin bereit", "translation": "Men tayyorman"},
{"word": "Wie spät ist es?", "translation": "Soat nechchi?"},
{"word": "Es ist drei Uhr", "translation": "Soat uch"},
{"word": "Heute ist Montag", "translation": "Bugun dushanba"},
{"word": "Ich mag Musik", "translation": "Men musiqani yoqtiraman"},
{"word": "Ich mag Fußball", "translation": "Men futbolni yoqtiraman"},
{"word": "Ich habe eine Katze", "translation": "Menda mushuk bor"},
{"word": "Ich habe einen Bruder", "translation": "Mening akam bor"},
{"word": "Ich habe keine Schwester", "translation": "Menda opa-singil yo‘q"},
{"word": "Mein Vater arbeitet", "translation": "Otam ishlaydi"},
{"word": "Meine Mutter kocht", "translation": "Onam ovqat pishiradi"},
{"word": "Ich gehe nach Hause", "translation": "Men uyga ketayapman"},
{"word": "Ich gehe in die Schule", "translation": "Men maktabga ketayapman"},
{"word": "Ich fahre mit dem Bus", "translation": "Men avtobusda ketayapman"},
{"word": "Ich lese ein Buch", "translation": "Men kitob o‘qiyapman"},
{"word": "Ich schreibe", "translation": "Men yozayapman"},
{"word": "Ich höre Musik", "translation": "Men musiqa tinglayapman"},
{"word": "Ich sehe fern", "translation": "Men televizor ko‘rayapman"},
{"word": "Ich schlafe", "translation": "Men uxlayapman"},
{"word": "Ich dusche", "translation": "Men dush qabul qilyapman"},
{"word": "Ich koche", "translation": "Men ovqat pishiraman"},
{"word": "Ich esse", "translation": "Men ovqatlanyapman"},
{"word": "Ich trinke Wasser", "translation": "Men suv ichayapman"},
{"word": "Ich öffne die Tür", "translation": "Men eshikni ochayapman"},
{"word": "Ich mache das Fenster auf", "translation": "Men derazani ochayapman"},
{"word": "Ich sehe dich", "translation": "Men seni ko‘ryapman"},
{"word": "Ich höre dich", "translation": "Men seni eshitayapman"},
{"word": "Ich warte", "translation": "Men kutayapman"},
{"word": "Ich komme gleich", "translation": "Men hozir kelaman"},
{"word": "Bis bald!", "translation": "Ko‘rishguncha!"},
{"word": "Bis morgen!", "translation": "Ertagacha!"},
{"word": "Viel Glück!", "translation": "Omad!"},
{"word": "Gute Reise!", "translation": "Yaxshi safar!"},
{"word": "Alles Gute zum Geburtstag!", "translation": "Tug‘ilgan kun bilan!"},
{"word": "Herzlichen Glückwunsch!", "translation": "Tabriklayman!"},
{"word": "Ich bin glücklich", "translation": "Men baxtliman"},
{"word": "Ich bin traurig", "translation": "Men xafaman"},
{"word": "Ich bin zufrieden", "translation": "Men mamnunman"},
{"word": "Ich habe Angst", "translation": "Men qo‘rqayapman"},
{"word": "Ich bin stolz", "translation": "Men faxrlanaman"},
{"word": "Ich bin nervös", "translation": "Men hayajondaman"},
{"word": "Ich bin ruhig", "translation": "Men tinchman"},
{"word": "Ich bin beschäftigt", "translation": "Men bandman"},
{"word": "Ich bin frei", "translation": "Men bo‘shman"},
{"word": "Ich gehe spazieren", "translation": "Men sayr qilayapman"},
{"word": "Ich wohne allein", "translation": "Men yolg‘iz yashayman"},
{"word": "Ich bin verheiratet", "translation": "Men uylanganman / turmush qurganman"},
{"word": "Ich bin ledig", "translation": "Men turmush qurmaganman"},
{"word": "Ich komme spät", "translation": "Men kech kelyapman"},
{"word": "Ich muss gehen", "translation": "Men ketishim kerak"},
{"word": "Ich verstehe dich gut", "translation": "Men seni yaxshi tushunaman"},
{"word": "Ich spreche ein bisschen Deutsch", "translation": "Men ozgina nemischa gapiraman"},
{"word": "Ich komme aus Usbekistan", "translation": "Men O'zbekistondanman"},
            {"word": "Sprechen Sie Englisch?", "translation": "Siz inglizcha gapirasizmi?"},
            {"word": "Ein bisschen", "translation": "Biroz"},
        ],
        "A2": [
            {"word": "Ich lerne Deutsch", "translation": "Men nemis tilini o'rganayapman"},
            {"word": "Ich wohne in Berlin", "translation": "Men Berlinda yashayman"},
            {"word": "Ich arbeite jeden Tag", "translation": "Men har kuni ishlayman"},
            {"word": "Kannst du das wiederholen?", "translation": "Buni takrorlab bera olasizmi?"},
            {"word": "Sprechen Sie bitte langsam", "translation": "Iltimos sekinroq gapiring"},
{"word": "Was machst du am Wochenende?", "translation": "Dam olish kunlari nima qilasan?"},
{"word": "Ich gehe einkaufen", "translation": "Men xarid qilaman"},
{"word": "Ich besuche meine Freunde", "translation": "Men do‘stlarimni ziyorat qilaman"},
{"word": "Ich bleibe zu Hause", "translation": "Men uyda qolaman"},
{"word": "Ich koche gerne", "translation": "Men ovqat pishirishni yoqtiraman"},
{"word": "Ich schaue einen Film", "translation": "Men film ko‘raman"},
{"word": "Ich spiele Fußball", "translation": "Men futbol o‘ynayman"},
{"word": "Ich gehe spazieren", "translation": "Men sayr qilaman"},
{"word": "Ich lese oft Bücher", "translation": "Men tez-tez kitob o‘qiyman"},
{"word": "Ich gehe früh ins Bett", "translation": "Men erta uxlashga yotaman"},
{"word": "Was ist dein Hobby?", "translation": "Sening hobbing nima?"},
{"word": "Mein Hobby ist Musik hören", "translation": "Mening hobbim musiqa tinglash"},
{"word": "Ich habe keine Zeit", "translation": "Menda vaqt yo‘q"},
{"word": "Ich bin beschäftigt", "translation": "Men bandman"},
{"word": "Ich bin müde, ich will schlafen", "translation": "Men charchadim, uxlashni xohlayman"},
{"word": "Ich arbeite jeden Tag", "translation": "Men har kuni ishlayman"},
{"word": "Ich habe Urlaub", "translation": "Men ta’tildaman"},
{"word": "Ich gehe in den Urlaub", "translation": "Men ta’tilga ketayapman"},
{"word": "Ich brauche eine Pause", "translation": "Menga tanaffus kerak"},
{"word": "Ich stehe um 7 Uhr auf", "translation": "Men soat 7 da turaman"},
{"word": "Ich frühstücke um 8 Uhr", "translation": "Men soat 8 da nonushta qilaman"},
{"word": "Ich gehe um 9 Uhr zur Arbeit", "translation": "Men soat 9 da ishga ketaman"},
{"word": "Ich esse zu Mittag um 13 Uhr", "translation": "Men soat 13 da tushlik qilaman"},
{"word": "Ich komme um 18 Uhr nach Hause", "translation": "Men soat 18 da uyga qaytaman"},
{"word": "Ich gehe um 23 Uhr ins Bett", "translation": "Men soat 23 da uxlashga yotaman"},
{"word": "Ich wohne mit meiner Familie", "translation": "Men oilam bilan yashayman"},
{"word": "Ich habe eine kleine Wohnung", "translation": "Menda kichik kvartira bor"},
{"word": "Mein Zimmer ist hell und sauber", "translation": "Mening xonam yorug‘ va toza"},
{"word": "Ich mag meine Nachbarn", "translation": "Men qo‘shnilarimni yoqtiraman"},
{"word": "Ich esse gerne Obst und Gemüse", "translation": "Men meva va sabzavotni yoqtiraman"},
{"word": "Ich trinke keinen Alkohol", "translation": "Men alkogol ichmayman"},
{"word": "Ich gehe oft ins Kino", "translation": "Men tez-tez kinoga boraman"},
{"word": "Ich lerne jeden Tag Deutsch", "translation": "Men har kuni nemis tili o‘rganaman"},
{"word": "Ich spreche ein bisschen Englisch", "translation": "Men ozgina inglizcha gapiraman"},
{"word": "Ich verstehe dich nicht gut", "translation": "Men seni yaxshi tushunmayapman"},
{"word": "Kannst du bitte langsamer sprechen?", "translation": "Iltimos, sekinroq gapira olasanmi?"},
{"word": "Wie spät ist es jetzt?", "translation": "Hozir soat nechchi?"},
{"word": "Es ist halb neun", "translation": "Soat sakkiz yarim"},
{"word": "Ich habe gestern gearbeitet", "translation": "Men kecha ishladim"},
{"word": "Ich bin nach Hause gegangen", "translation": "Men uyga ketdim"},
{"word": "Ich habe meine Freunde getroffen", "translation": "Men do‘stlarim bilan uchrashdim"},
{"word": "Ich bin ins Kino gegangen", "translation": "Men kinoga bordim"},
{"word": "Ich habe ein Buch gelesen", "translation": "Men bir kitob o‘qidim"},
{"word": "Ich habe Musik gehört", "translation": "Men musiqa tingladim"},
{"word": "Ich habe gekocht", "translation": "Men ovqat pishirdim"},
{"word": "Ich habe ferngesehen", "translation": "Men televizor ko‘rdim"},
{"word": "Ich bin spät aufgestanden", "translation": "Men kech uyg‘ondim"},
{"word": "Ich habe lange geschlafen", "translation": "Men uzoq uxladim"},
{"word": "Ich bin ins Bett gegangen", "translation": "Men yotdim"},
{"word": "Ich habe Kaffee getrunken", "translation": "Men qahva ichdim"},
{"word": "Ich bin mit dem Bus gefahren", "translation": "Men avtobusda bordim"},
{"word": "Ich habe mein Handy vergessen", "translation": "Men telefonimni unutdim"},
{"word": "Ich habe Hunger", "translation": "Men ochman"},
{"word": "Ich will etwas essen", "translation": "Men nimadir yemoqchiman"},
{"word": "Ich möchte ein Brot", "translation": "Men non xohlayman"},
{"word": "Ich möchte eine Suppe", "translation": "Men sho‘rva xohlayman"},
{"word": "Ich möchte Wasser trinken", "translation": "Men suv ichmoqchiman"},
{"word": "Was empfehlen Sie?", "translation": "Siz nima tavsiya qilasiz?"},
{"word": "Die Rechnung, bitte", "translation": "Hisobni bering, iltimos"},
{"word": "Wo ist die Toilette?", "translation": "Hojatxona qayerda?"},
{"word": "Ich suche den Bahnhof", "translation": "Men vokzalni qidirmoqdaman"},
{"word": "Ich brauche ein Taxi", "translation": "Menga taksi kerak"},
{"word": "Wie komme ich zum Flughafen?", "translation": "Aeroportga qanday boraman?"},
{"word": "Ich wohne in der Nähe", "translation": "Men yaqin joyda yashayman"},
{"word": "Ich bin zu spät", "translation": "Men kechikdim"},
{"word": "Ich bin pünktlich", "translation": "Men o‘z vaqtida keldim"},
{"word": "Ich mag deinen Stil", "translation": "Menga senga uslubing yoqadi"},
{"word": "Ich bin glücklich mit meinem Leben", "translation": "Men hayotimdan baxtliman"},
{"word": "Ich will gesund leben", "translation": "Men sog‘lom yashamoqchiman"},
{"word": "Ich gehe oft joggen", "translation": "Men tez-tez yugurishga chiqaman"},
{"word": "Ich mache Sport", "translation": "Men sport bilan shug‘ullanaman"},
{"word": "Ich esse kein Fleisch", "translation": "Men go‘sht yemayman"},
{"word": "Ich brauche Ruhe", "translation": "Menga tinchlik kerak"},
{"word": "Ich habe Fieber", "translation": "Men isitmam bor"},
{"word": "Ich muss zum Arzt gehen", "translation": "Men doktorga borishim kerak"},
{"word": "Ich nehme Medikamente", "translation": "Men dori ichayapman"},
{"word": "Ich bin erkältet", "translation": "Men shamollaganman"},
{"word": "Ich bleibe im Bett", "translation": "Men yotoqda qolaman"},
{"word": "Ich habe keine Lust", "translation": "Mening xohishim yo‘q"},
{"word": "Ich bin bereit zu lernen", "translation": "Men o‘rganishga tayyorman"},
{"word": "Ich mache Fortschritte", "translation": "Men yutuqlarga erishyapman"},
{"word": "Ich lerne neue Wörter", "translation": "Men yangi so‘zlarni o‘rganayapman"},
{"word": "Ich verstehe mehr als früher", "translation": "Men avvalgidan ko‘proq tushunayapman"},
{"word": "Ich bin stolz auf mich", "translation": "Men o‘zimdan faxrlanaman"},
            {"word": "Wo ist das Krankenhaus?", "translation": "Shifoxona qayerda?"},
        ],
        "B1": [
            {"word": "möchten", "translation": "xohlash (xohlayman)"},
            {"word": "erzählen", "translation": "so‘zlab bermoq"},
            {"word": "beschreiben", "translation": "tasvirlamoq"},
            {"word": "lernen", "translation": "o'rganmoq"},
            {"word": "versuchen", "translation": "harakat qilmoq"},
            {"word": "verstehen", "translation": "tushunmoq"},
            {"word": "reichen", "translation": "yetmoq"},
            {"word": "Ich interessiere mich für Musik", "translation": "Men musiqaga qiziqaman"},
{"word": "Ich möchte im Ausland arbeiten", "translation": "Men chet elda ishlamoqchiman"},
{"word": "Ich habe viele neue Leute kennengelernt", "translation": "Men ko‘p yangi odamlar bilan tanishdim"},
{"word": "Ich möchte meine Sprachkenntnisse verbessern", "translation": "Men til ko‘nikmalarimni yaxshilamoqchiman"},
{"word": "Ich lese jeden Tag Nachrichten", "translation": "Men har kuni yangiliklar o‘qiyman"},
{"word": "Ich benutze oft das Internet", "translation": "Men tez-tez internetdan foydalanaman"},
{"word": "Ich möchte ein neues Handy kaufen", "translation": "Men yangi telefon sotib olmoqchiman"},
{"word": "Ich spare Geld für eine Reise", "translation": "Men sayohat uchun pul yig‘ayapman"},
{"word": "Ich habe gestern meine Hausaufgaben gemacht", "translation": "Men kecha uy vazifamni bajardim"},
{"word": "Ich habe letzte Woche Sport getrieben", "translation": "Men o‘tgan hafta sport bilan shug‘ullandim"},
{"word": "Ich bin ins Kino gegangen", "translation": "Men kinoga bordim"},
{"word": "Ich war sehr müde, aber glücklich", "translation": "Men charchagan edim, lekin baxtli edim"},
{"word": "Ich bin mit dem Zug nach Berlin gefahren", "translation": "Men poyezdda Berlindan bordim"},
{"word": "Ich habe ein interessantes Buch gelesen", "translation": "Men qiziqarli kitob o‘qidim"},
{"word": "Ich möchte ein Auto kaufen", "translation": "Men mashina sotib olmoqchiman"},
{"word": "Ich arbeite in einem Büro", "translation": "Men ofisda ishlayman"},
{"word": "Ich bin sehr zufrieden mit meiner Arbeit", "translation": "Men ishimdan juda mamnunman"},
{"word": "Ich habe eine wichtige Besprechung morgen", "translation": "Men ertaga muhim yig‘ilishga egaman"},
{"word": "Ich bereite mich auf die Prüfung vor", "translation": "Men imtihonga tayyorlanayapman"},
{"word": "Ich habe die Prüfung bestanden", "translation": "Men imtihonni topshirdim"},
{"word": "Ich möchte an der Universität studieren", "translation": "Men universitetda o‘qimoqchiman"},
{"word": "Ich studiere Informatik", "translation": "Men informatika yo‘nalishida o‘qiyman"},
{"word": "Ich lerne Deutsch, weil ich in Deutschland leben möchte", "translation": "Men nemis tili o‘rganayapman, chunki Germaniyada yashamoqchiman"},
{"word": "Ich reise gern", "translation": "Men sayohat qilishni yoqtiraman"},
{"word": "Ich war schon einmal in Österreich", "translation": "Men Avstriyada bo‘lganman"},
{"word": "Ich möchte nächstes Jahr nach Deutschland reisen", "translation": "Men kelasi yili Germaniyaga sayohat qilmoqchiman"},
{"word": "Ich mag die deutsche Kultur", "translation": "Menga nemis madaniyati yoqadi"},
{"word": "Ich finde Deutsch eine interessante Sprache", "translation": "Menimcha, nemis tili qiziqarli til"},
{"word": "Ich esse gern traditionelles Essen", "translation": "Men an’anaviy taomlarni yoqtiraman"},
{"word": "Ich habe gestern gekocht", "translation": "Men kecha ovqat pishirdim"},
{"word": "Ich habe meine Freunde eingeladen", "translation": "Men do‘stlarimni taklif qildim"},
{"word": "Wir haben zusammen gegessen und gelacht", "translation": "Biz birga ovqat yedik va kuldik"},
{"word": "Ich möchte mehr Sport treiben", "translation": "Men ko‘proq sport bilan shug‘ullanmoqchiman"},
{"word": "Ich gehe dreimal pro Woche ins Fitnessstudio", "translation": "Men haftasiga uch marta sport zaliga boraman"},
{"word": "Gesundheit ist sehr wichtig für mich", "translation": "Sog‘liq men uchun juda muhim"},
{"word": "Ich achte auf meine Ernährung", "translation": "Men ovqatlanishimga e’tibor beraman"},
{"word": "Ich trinke viel Wasser", "translation": "Men ko‘p suv ichaman"},
{"word": "Ich esse wenig Zucker", "translation": "Men kam shakar iste’mol qilaman"},
{"word": "Ich schlafe acht Stunden pro Nacht", "translation": "Men kechasi sakkiz soat uxlayman"},
{"word": "Ich verbringe viel Zeit mit meiner Familie", "translation": "Men oilam bilan ko‘p vaqt o‘tkazaman"},
{"word": "Ich habe zwei Geschwister", "translation": "Mening ikki nafar aka-ukam/opa-singlim bor"},
{"word": "Ich helfe meiner Mutter im Haushalt", "translation": "Men onamga uy ishlarida yordam beraman"},
{"word": "Ich kümmere mich um meinen kleinen Bruder", "translation": "Men kichik ukamga qarayman"},
{"word": "Ich telefoniere oft mit meinen Freunden", "translation": "Men do‘stlarim bilan tez-tez telefonlashaman"},
{"word": "Ich gehe am Wochenende oft spazieren", "translation": "Men dam olish kunlari sayrga chiqaman"},
{"word": "Ich mag Tiere, besonders Katzen", "translation": "Men hayvonlarni yoqtiraman, ayniqsa mushuklarni"},
{"word": "Ich habe einen Hund", "translation": "Menda it bor"},
{"word": "Ich gehe jeden Morgen mit meinem Hund spazieren", "translation": "Men har kuni ertalab itim bilan sayr qilaman"},
{"word": "Ich möchte in der Zukunft ein Haus kaufen", "translation": "Men kelajakda uy sotib olmoqchiman"},
{"word": "Ich träume von einem ruhigen Leben", "translation": "Men tinch hayot haqida orzu qilaman"},
{"word": "Ich möchte glücklich sein", "translation": "Men baxtli bo‘lishni xohlayman"},
{"word": "Ich bin stolz auf meine Familie", "translation": "Men oilamdan faxrlanaman"},
{"word": "Ich will erfolgreich im Leben sein", "translation": "Men hayotda muvaffaqiyatli bo‘lishni xohlayman"},
{"word": "Ich plane meine Zukunft sorgfältig", "translation": "Men kelajagimni ehtiyotkorlik bilan rejalashtiraman"},
{"word": "Ich glaube an mich selbst", "translation": "Men o‘zimga ishonaman"},
{"word": "Ich gebe nie auf", "translation": "Men hech qachon taslim bo‘lmayman"},
{"word": "Ich möchte die Welt bereisen", "translation": "Men butun dunyoni kezmoqchiman"},
{"word": "Ich liebe es, neue Dinge zu lernen", "translation": "Men yangi narsalarni o‘rganishni yoqtiraman"},
{"word": "Ich höre gerne Podcasts", "translation": "Men podkastlarni tinglashni yoqtiraman"},
{"word": "Ich sehe selten fern", "translation": "Men kamdan-kam televizor ko‘raman"},
{"word": "Ich benutze mein Handy zu viel", "translation": "Men telefonimdan juda ko‘p foydalanaman"},
{"word": "Ich versuche, weniger Zeit im Internet zu verbringen", "translation": "Men internetda kamroq vaqt o‘tkazishga harakat qilaman"},
{"word": "Ich möchte produktiver sein", "translation": "Men samaraliroq bo‘lishni xohlayman"},
{"word": "Ich bin sehr motiviert", "translation": "Men juda motivatsiyalanganman"},
{"word": "Ich konzentriere mich auf meine Ziele", "translation": "Men maqsadlarimga e’tibor qarataman"},
{"word": "Ich arbeite hart für meinen Erfolg", "translation": "Men muvaffaqiyatim uchun mehnat qilaman"},
{"word": "Ich glaube, dass alles möglich ist", "translation": "Men hamma narsa mumkinligiga ishonaman"},
{"word": "Ich habe Vertrauen in die Zukunft", "translation": "Men kelajakka ishonaman"},
{"word": "Ich bin dankbar für alles", "translation": "Men hamma narsa uchun minnatdorman"},
{"word": "Ich bin bereit für neue Herausforderungen", "translation": "Men yangi sinovlarga tayyorman"},
{"word": "Ich lerne aus meinen Fehlern", "translation": "Men xatolarimdan o‘rganaman"},
{"word": "Ich will mich jeden Tag verbessern", "translation": "Men har kuni o‘zimni yaxshilamoqchiman"},
{"word": "Ich helfe gern anderen Menschen", "translation": "Men boshqalarga yordam berishni yoqtiraman"},
{"word": "Ich respektiere andere Meinungen", "translation": "Men boshqalar fikrini hurmat qilaman"},
{"word": "Ich bin freundlich und ehrlich", "translation": "Men samimiy va halolman"},
{"word": "Ich glaube an Freundschaft", "translation": "Men do‘stlikka ishonaman"},
{"word": "Ich mag es, mit Leuten zu reden", "translation": "Men odamlar bilan suhbatlashishni yoqtiraman"},
{"word": "Ich genieße jeden Tag", "translation": "Men har bir kunni qadrlayman"},
{"word": "Ich lache viel", "translation": "Men ko‘p kulaman"},
{"word": "Ich bin glücklich, wenn die Sonne scheint", "translation": "Quyosh chiqqanda men baxtliman"},
{"word": "Ich liebe das Leben", "translation": "Men hayotni sevaman"},
            {"word": "gefallen", "translation": "yoqmoq"},
            {"word": "vergessen", "translation": "unutmoq"},
            {"word": "sich erinnern", "translation": "eslash"},
        ],
        "B2": [
            {"word": "kommunizieren", "translation": "muloqot qilmoq"},
            {"word": "erweitern", "translation": "kengaytirmoq"},
            {"word": "die Kultur", "translation": "madaniyat"},
            {"word": "Ich bin der Meinung, dass Bildung sehr wichtig ist", "translation": "Menimcha, ta’lim juda muhim"},
{"word": "Meiner Ansicht nach sollte jeder eine Fremdsprache lernen", "translation": "Mening fikrimcha, har bir inson chet tili o‘rganishi kerak"},
{"word": "Es ist allgemein bekannt, dass Bewegung gesund ist", "translation": "Hamma biladiki, harakat sog‘lomlik uchun foydali"},
{"word": "Ich bin überzeugt, dass Technologie unser Leben erleichtert", "translation": "Men ishonamanki, texnologiya hayotimizni yengillashtiradi"},
{"word": "Ich finde es wichtig, umweltbewusst zu leben", "translation": "Men uchun atrof-muhitga e’tiborli yashash muhim"},
{"word": "Einerseits ist das Leben in der Stadt bequem, andererseits laut", "translation": "Bir tomondan shaharda yashash qulay, boshqa tomondan esa shovqinli"},
{"word": "Ich versuche, meine Zeit sinnvoll zu nutzen", "translation": "Men vaqtimni foydali ishlarga sarflashga harakat qilaman"},
{"word": "Ich denke, dass soziale Medien sowohl Vorteile als auch Nachteile haben", "translation": "Menimcha, ijtimoiy tarmoqlarning foyda ham, zarar ham bor"},
{"word": "In meiner Kindheit habe ich viel draußen gespielt", "translation": "Bolaligimda men tashqarida ko‘p o‘ynardim"},
{"word": "Ich habe gelernt, Verantwortung zu übernehmen", "translation": "Men javobgarlikni o‘z zimmamga olishni o‘rgandim"},
{"word": "Ich bin stolz auf das, was ich erreicht habe", "translation": "Men erishgan narsalarimdan faxrlanaman"},
{"word": "Ich glaube, dass Fehler zum Lernen gehören", "translation": "Menimcha, xatolar o‘rganish jarayonining bir qismidir"},
{"word": "Ich versuche immer, positiv zu denken", "translation": "Men har doim ijobiy fikrlashga harakat qilaman"},
{"word": "Ich habe vor, in Zukunft im Ausland zu arbeiten", "translation": "Men kelajakda chet elda ishlashni rejalashtirayapman"},
{"word": "Ich interessiere mich für Psychologie und Kommunikation", "translation": "Men psixologiya va muloqotga qiziqaman"},
{"word": "Es ist mir wichtig, meine Ziele zu erreichen", "translation": "Men uchun maqsadlarimga erishish muhim"},
{"word": "Ich bin dankbar für die Erfahrungen, die ich gesammelt habe", "translation": "Men to‘plagan tajribalarim uchun minnatdorman"},
{"word": "Ich glaube, dass Erfolg harte Arbeit und Geduld erfordert", "translation": "Menimcha, muvaffaqiyat mehnat va sabrni talab qiladi"},
{"word": "Ich habe gelernt, Probleme selbstständig zu lösen", "translation": "Men muammolarni mustaqil hal qilishni o‘rgandim"},
{"word": "Ich halte es für notwendig, jeden Tag etwas Neues zu lernen", "translation": "Men har kuni yangi narsa o‘rganish zarur deb hisoblayman"},
{"word": "Ich habe festgestellt, dass Lesen meinen Wortschatz verbessert", "translation": "Men o‘qish so‘z boyligimni oshirishini sezdim"},
{"word": "Ich möchte meine Kommunikationsfähigkeiten verbessern", "translation": "Men muloqot qobiliyatlarimni yaxshilamoqchiman"},
{"word": "Ich bin bereit, neue Herausforderungen anzunehmen", "translation": "Men yangi sinovlarni qabul qilishga tayyorman"},
{"word": "Ich schätze Menschen, die ehrlich und zuverlässig sind", "translation": "Men halol va ishonchli insonlarni qadrlayman"},
{"word": "Ich habe gelernt, mit Stress umzugehen", "translation": "Men stressni boshqarishni o‘rgandim"},
{"word": "Ich versuche, Kritik positiv zu sehen", "translation": "Men tanqidni ijobiy qabul qilishga harakat qilaman"},
{"word": "Ich finde, dass Reisen die beste Bildung ist", "translation": "Menimcha, sayohat eng yaxshi ta’limdir"},
{"word": "Ich habe erkannt, wie wichtig Gesundheit ist", "translation": "Men sog‘liqning naqadar muhimligini angladim"},
{"word": "Ich bin zufrieden, wenn ich etwas Sinnvolles tue", "translation": "Men foydali ish qilganimda mamnun bo‘laman"},
{"word": "Ich glaube, dass Glück von innen kommt", "translation": "Menimcha, baxt ichimizdan keladi"},
{"word": "Ich versuche, in schwierigen Situationen ruhig zu bleiben", "translation": "Men qiyin vaziyatlarda xotirjam bo‘lishga harakat qilaman"},
{"word": "Ich denke, dass Kommunikation der Schlüssel zum Erfolg ist", "translation": "Menimcha, muloqot muvaffaqiyat kalitidir"},
{"word": "Ich möchte mein eigenes Unternehmen gründen", "translation": "Men o‘z biznesimni ochmoqchiman"},
{"word": "Ich interessiere mich für moderne Technologien", "translation": "Men zamonaviy texnologiyalarga qiziqaman"},
{"word": "Ich finde, dass Teamarbeit sehr wichtig ist", "translation": "Menimcha, jamoada ishlash juda muhim"},
{"word": "Ich habe gelernt, Kompromisse zu schließen", "translation": "Men kelishuvga erishishni o‘rgandim"},
{"word": "Ich bin neugierig auf andere Kulturen", "translation": "Men boshqa madaniyatlarga qiziqaman"},
{"word": "Ich glaube, dass man durch Fehler wächst", "translation": "Menimcha, inson xatolari orqali o‘sadi"},
{"word": "Ich versuche, meine Meinung respektvoll auszudrücken", "translation": "Men fikrimni hurmat bilan bildirishga harakat qilaman"},
{"word": "Ich denke oft über die Zukunft nach", "translation": "Men ko‘p hollarda kelajak haqida o‘ylayman"},
{"word": "Ich finde es spannend, neue Sprachen zu lernen", "translation": "Men yangi tillarni o‘rganishni hayajonli deb bilaman"},
{"word": "Ich habe gelernt, Verantwortung zu übernehmen", "translation": "Men javobgarlikni o‘z zimmamga olishni o‘rgandim"},
{"word": "Ich achte darauf, genug Zeit für mich selbst zu haben", "translation": "Men o‘zim uchun yetarli vaqt ajratishga e’tibor beraman"},
{"word": "Ich halte Freundschaft für sehr wertvoll", "translation": "Men do‘stlikni juda qimmatli deb bilaman"},
{"word": "Ich bin überzeugt, dass kleine Dinge einen großen Unterschied machen", "translation": "Men ishonamanki, kichik narsalar katta o‘zgarishlar keltiradi"},
{"word": "Ich habe gelernt, im Team zu arbeiten", "translation": "Men jamoada ishlashni o‘rgandim"},
{"word": "Ich finde, dass man nie aufhören sollte zu lernen", "translation": "Menimcha, inson hech qachon o‘rganishni to‘xtatmasligi kerak"},
{"word": "Ich glaube, dass Kommunikation Vertrauen aufbaut", "translation": "Menimcha, muloqot ishonchni kuchaytiradi"},
{"word": "Ich versuche, aus jeder Erfahrung etwas zu lernen", "translation": "Men har bir tajribadan nimadir o‘rganishga harakat qilaman"},
{"word": "Ich denke, dass Respekt die Basis jeder Beziehung ist", "translation": "Menimcha, hurmat har qanday munosabatning asosi"},
{"word": "Ich bin froh, neue Chancen zu bekommen", "translation": "Men yangi imkoniyatlarga ega bo‘lganimdan xursandman"},
{"word": "Ich möchte die Welt positiv beeinflussen", "translation": "Men dunyoga ijobiy ta’sir ko‘rsatmoqchiman"},
            {"word": "die Perspektive", "translation": "qarash"},
        ],
    },
}

# ---------------------------
#  DATA helpers
# ---------------------------
def load_data() -> Dict[str, Any]:
    if not os.path.exists(DATA_FILE):
        return {}
    try:
        with open(DATA_FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    except Exception:
        return {}

def save_data(data: Dict[str, Any]):
    with open(DATA_FILE, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=2)

def ensure_user(data: Dict[str, Any], uid: str, name: str = None):
    if uid not in data:
        data[uid] = {
            "name": name or "Foydalanuvchi",
            "learn_lang": None,   # 'english' or 'german'
            "target_lang": "uzbek",  # translation language (uzbek by default)
            "level": None,        # 'A1','A2','B1','B2'
            "progress": 0,        # index in lesson list (0-based)
            "score": 0,
            "learned": [],        # list of learned words (for revision)
            "daily_queue": [],    # words scheduled for today
            "daily_opt_out": False,
        }

# ---------------------------
#  Keyboards
# ---------------------------
def lang_menu():
    kb = [
        [InlineKeyboardButton("🇬🇧 English", callback_data="learn_english"),
         InlineKeyboardButton("🇩🇪 Deutsch", callback_data="learn_german")],
    ]
    return InlineKeyboardMarkup(kb)

def target_lang_menu():
    kb = [
        [InlineKeyboardButton("🇺🇿 Uzbek", callback_data="target_uzbek"),
         InlineKeyboardButton("🇷🇺 Russian", callback_data="target_russian"),
         InlineKeyboardButton("🇬🇧 English", callback_data="target_english")],
    ]
    return InlineKeyboardMarkup(kb)

def level_menu():
    kb = [
        [InlineKeyboardButton("A1", callback_data="level_A1"),
         InlineKeyboardButton("A2", callback_data="level_A2")],
        [InlineKeyboardButton("B1", callback_data="level_B1"),
         InlineKeyboardButton("B2", callback_data="level_B2")],
    ]
    return InlineKeyboardMarkup(kb)

def study_menu():
    kb = [
        [InlineKeyboardButton("📚 Darsni boshlash", callback_data="start_lesson"),
         InlineKeyboardButton("⏩ Davom etish", callback_data="continue_lesson")],
        [InlineKeyboardButton("🧩 Test", callback_data="start_test"),
         InlineKeyboardButton("🔁 Takrorlash", callback_data="revision")],
        [InlineKeyboardButton("🏆 Ball", callback_data="my_score"),
         InlineKeyboardButton("⭐ TOP10", callback_data="top10")],
        [InlineKeyboardButton("⚙️ Sozlamalar", callback_data="settings")]
    ]
    return InlineKeyboardMarkup(kb)

# ---------------------------
#  Commands
# ---------------------------
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.effective_user
    data = load_data()
    uid = str(user.id)
    ensure_user(data, uid, user.first_name)
    save_data(data)
    await update.message.reply_text(
        f"Salom, {user.first_name}! 👋\n"
        "Til o'rganish botiga xush kelibsiz.\n"
        "Qaysi tilni o'rganmoqchisiz?",
        reply_markup=lang_menu()
    )

async def help_cmd(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text(
        "/start — boshidan boshlash\n"
        "/profile — profilni ko'rish\n"
        "/top10 — hozirgi TOP10\n"
        "/stopdaily — kundalik jo'natishni o'chirish\n"
        "/startdaily — kundalik jo'natishni yoqish"
    )

async def profile(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.effective_user
    data = load_data()
    uid = str(user.id)
    if uid not in data:
        await update.message.reply_text("Avval /start ni bosing.")
        return
    info = data[uid]
    await update.message.reply_text(
        f"Ism: {info.get('name')}\n"
        f"Til: {info.get('learn_lang')}\n"
        f"Tarjima tili: {info.get('target_lang')}\n"
        f"Daraja: {info.get('level')}\n"
        f"Progress: {info.get('progress')}\n"
        f"Ball: {info.get('score')}"
    )

# ---------------------------
#  Button handler
# ---------------------------
async def button_handler(update: Update, context: ContextTypes.DEFAULT_TYPE):
    query = update.callback_query
    await query.answer()
    data = load_data()
    uid = str(query.from_user.id)
    ensure_user(data, uid, query.from_user.first_name)
    user = data[uid]
    payload = query.data

    # Language selection
    if payload.startswith("learn_"):
        user["learn_lang"] = payload.split("_", 1)[1]
        user["level"] = None
        user["progress"] = 0
        save_data(data)
        await query.edit_message_text("Qaysi tilda tarjima qilinsin?", reply_markup=target_lang_menu())
        return

    # target language selection
    if payload.startswith("target_"):
        target = payload.split("_", 1)[1]
        user["target_lang"] = target
        save_data(data)
        await query.edit_message_text("Darajani tanlang:", reply_markup=level_menu())
        return

    # level selection
    if payload.startswith("level_"):
        level = payload.split("_", 1)[1]
        user["level"] = level
        user["progress"] = 0
        user["daily_queue"] = []
        save_data(data)
        await query.edit_message_text(f"✅ Tanlandi: {user['learn_lang'].capitalize()} {level}", reply_markup=study_menu())
        return

    # start lesson (reset progress)
    if payload == "start_lesson":
        if not user.get("learn_lang") or not user.get("level"):
            await query.edit_message_text("Iltimos, avval til va darajani tanlang.", reply_markup=lang_menu())
            return
        user["progress"] = 0
        save_data(data)
        await send_current_word(uid, query, data)
        return

    # continue lesson (from progress)
    if payload == "continue_lesson":
        if not user.get("learn_lang") or not user.get("level"):
            await query.edit_message_text("Iltimos, avval til va darajani tanlang.", reply_markup=lang_menu())
            return
        await send_current_word(uid, query, data)
        return

    # revision (last learned)
    if payload == "revision":
        learned = user.get("learned", [])[-15:]
        if not learned:
            await query.edit_message_text("Hozircha o'rganilgan so'zlar mavjud emas.", reply_markup=study_menu())
            return
        text = "🔁 So'nggi o'rganilganlar:\n\n"
        for obj in learned:
            text += f"{obj['word']} → {obj['translation']}\n"
        await query.edit_message_text(text, reply_markup=study_menu())
        return

    # start test
    if payload == "start_test":
        if not user.get("learn_lang") or not user.get("level"):
            await query.edit_message_text("Iltimos til va darajani tanlang.", reply_markup=lang_menu())
            return
        await start_test_for_user(uid, query, data)
        return

    # answer handling: ans_{chosen}_{correct}
    if payload.startswith("ans_"):
        try:
            _, chosen, correct = payload.split("_", 2)
        except ValueError:
            await query.answer("Noto'g'ri format", show_alert=False)
            return
        if chosen == correct:
            user["score"] = user.get("score", 0) + 10
            reply = f"✅ To'g'ri! +10 ball\nJami ball: {user['score']}"
        else:
            reply = f"❌ Noto'g'ri. To'g'ri: {correct}\nJami ball: {user.get('score',0)}"
        data[uid] = user
        save_data(data)
        await query.edit_message_text(reply, reply_markup=study_menu())
        return

    # my score
    if payload == "my_score":
        await query.edit_message_text(f"🏆 Jami ballingiz: {user.get('score',0)}", reply_markup=study_menu())
        return

    # top10
    if payload == "top10":
        await send_top10_direct(query, data)
        return

    # settings
    if payload == "settings":
        await query.edit_message_text("⚙️ Sozlamalar:\n/start orqali qaytadan sozlashingiz mumkin.", reply_markup=study_menu())
        return

    # fallback
    await query.edit_message_text("Asosiy menyu:", reply_markup=study_menu())

# ---------------------------
#  Lesson flow: send current word (continue from progress)
# ---------------------------
async def send_current_word(uid: str, query_obj, data: Dict[str, Any]):
    user = data.get(uid)
    if not user:
        return
    lang = user.get("learn_lang")
    level = user.get("level")
    if not lang or not level:
        await query_obj.edit_message_text("Iltimos, til va darajani tanlang.", reply_markup=lang_menu())
        return
    pool = lessons.get(lang, {}).get(level, [])
    if not pool:
        await query_obj.edit_message_text("Darslar topilmadi. Administrator bilan bog'laning.", reply_markup=study_menu())
        return
    idx = user.get("progress", 0)
    if idx >= len(pool):
        await query_obj.edit_message_text("🎉 Siz bu darajadagi barcha darslarni ko‘rdingiz! Menyuga qayting.", reply_markup=study_menu())
        return
    item = pool[idx]
    # keyboard: next, menu, test
    kb = InlineKeyboardMarkup([
        [InlineKeyboardButton("➡️ Keyingi", callback_data="continue_lesson"),
         InlineKeyboardButton("🏠 Menuga", callback_data="settings")],
        [InlineKeyboardButton("🧩 Test bu so'zdan", callback_data="start_test")]
    ])
    # add to learned and increment progress
    learned = user.get("learned", [])
    learned.append(item)
    user["learned"] = learned[-500:]
    user["progress"] = idx + 1
    data[uid] = user
    save_data(data)
    # show text
    text = f"📘 {lang.capitalize()} {level}\n\n{item['word']} → {item['translation']}\n\n" \
           f"Oxirgi joy: {user['progress']}/{len(pool)}"
    await query_obj.edit_message_text(text, reply_markup=kb)

# ---------------------------
#  Test logic
# ---------------------------
async def start_test_for_user(uid: str, query_obj, data: Dict[str, Any]):
    user = data.get(uid)
    if not user:
        return
    lang = user.get("learn_lang")
    level = user.get("level")
    pool = lessons.get(lang, {}).get(level, [])
    if not pool:
        await query_obj.edit_message_text("Test uchun dars topilmadi.", reply_markup=study_menu())
        return
    q = random.choice(pool)
    correct = q["translation"]
    options = [correct]
    # add distractors
    while len(options) < 4:
        cand = random.choice(pool)["translation"]
        if cand not in options:
            options.append(cand)
    random.shuffle(options)
    kb = InlineKeyboardMarkup([[InlineKeyboardButton(opt, callback_data=f"ans_{opt}_{correct}")] for opt in options])
    await query_obj.edit_message_text(f"🧩 Test: {q['word']}\nQaysi tarjima to'g'ri?", reply_markup=kb)

# ---------------------------
#  TOP10 helper
# ---------------------------
async def send_top10_direct(query_obj, data: Dict[str, Any]):
    sorted_users = sorted(data.items(), key=lambda x: x[1].get("score", 0), reverse=True)
    top = sorted_users[:10]
    text = "⭐ TOP 10 foydalanuvchilar:\n\n"
    for i, (uid, info) in enumerate(top, start=1):
        text += f"{i}. {info.get('name','Noma\'lum')} — {info.get('score',0)} ball\n"
    await query_obj.edit_message_text(text, reply_markup=study_menu())

# ---------------------------
#  DAILY broadcast: send users daily words (and motivational reminders)
# ---------------------------
async def broadcast_daily_words(context: ContextTypes.DEFAULT_TYPE):
    tz = pytz.timezone(TIMEZONE)
    data = load_data()
    for uid, info in data.items():
        try:
            if info.get("daily_opt_out"):
                continue
            lang = info.get("learn_lang")
            level = info.get("level")
            if not lang or not level:
                # send reminder to choose language/level
                await context.bot.send_message(
                    chat_id=int(uid),
                    text="👋 Til va daraja tanlanmagan. /start yordamida til va darajangizni tanlang."
                )
                continue
            pool = lessons.get(lang, {}).get(level, [])
            if not pool:
                continue
            # determine start index (rotate through pool using progress)
            start_idx = info.get("progress", 0)
            queue = []
            for i in range(DAILY_WORDS_COUNT):
                idx = (start_idx + i) % len(pool)
                queue.append(pool[idx])
            info["daily_queue"] = queue
            data[uid] = info
            # send motivational message + today's words
            text = "📅 Bugungi so'zlar — Tez o'rganish:\n\n"
            for i, obj in enumerate(queue, start=1):
                text += f"{i}. {obj['word']} → {obj['translation']}\n"
            text += ("\n🔁 So'ngra botga qaytib 'Takrorlash' yoki 'Test' tugmasini bosing.\n"
                     "💡 Har to‘g‘ri javob +10 ball. Davom eting!")
            await context.bot.send_message(chat_id=int(uid), text=text)
        except Exception:
            # user might have blocked bot or other error — ignore
            pass
    save_data(data)

async def broadcast_motivational(context: ContextTypes.DEFAULT_TYPE):
    # Send a short motivational reminder to users who didn't finish daily queue
    data = load_data()
    for uid, info in data.items():
        try:
            if info.get("daily_opt_out"):
                continue
            # if user has daily_queue and hasn't completed enough progress, nudge them
            dq = info.get("daily_queue", [])
            # we don't track per-day completion in this simple version; send a generic nudge
            await context.bot.send_message(chat_id=int(uid), text="🔥 Bugun 5 daqiqa til o'rgansangiz kifoya! /start orqali davom eting.")
        except Exception:
            pass

async def broadcast_top10(context: ContextTypes.DEFAULT_TYPE):
    data = load_data()
    sorted_users = sorted(data.items(), key=lambda x: x[1].get("score", 0), reverse=True)[:10]
    if not sorted_users:
        return
    text = "🏆 Kundalik TOP 10 — eng faol o'rganuvchilar:\n\n"
    for i, (uid, info) in enumerate(sorted_users, start=1):
        text += f"{i}. {info.get('name','Noma\'lum')} — {info.get('score',0)} ball\n"
    for uid in data.keys():
        try:
            await context.bot.send_message(chat_id=int(uid), text=text)
        except Exception:
            pass

# ---------------------------
#  Start/stop daily commands
# ---------------------------
async def stopdaily(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.effective_user
    data = load_data()
    uid = str(user.id)
    if uid in data:
        data[uid]["daily_opt_out"] = True
        save_data(data)
    await update.message.reply_text("Siz kundalik jo'natishdan voz kechdingiz. /startdaily bilan qayta yoqishingiz mumkin.")

async def startdaily(update: Update, context: ContextTypes.DEFAULT_TYPE):
    user = update.effective_user
    data = load_data()
    uid = str(user.id)
    if uid in data:
        data[uid]["daily_opt_out"] = False
        save_data(data)
    await update.message.reply_text("Siz kundalik so'zlar jo'natishni qayta yoqtirdingiz.")

# ---------------------------
#  Message handler
# ---------------------------
async def text_handler(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("📝 Iltimos menyulardan foydalaning yoki /start ni bosing.", reply_markup=lang_menu())

# ---------------------------
#  Scheduler setup
# ---------------------------
def schedule_jobs(app):
    tz = pytz.timezone(TIMEZONE)
    # daily words at first hour in list (09:00)
    app.job_queue.run_daily(
        broadcast_daily_words,
        time=time(hour=DAILY_SEND_HOURS[0], minute=0, tzinfo=tz),
        name="daily_words"
    )
    # motivational reminders at other hours
    for hr in DAILY_SEND_HOURS[1:]:
        app.job_queue.run_daily(
            broadcast_motivational,
            time=time(hour=hr, minute=0, tzinfo=tz),
            name=f"motivate_{hr}"
        )
    # top10 broadcast
    app.job_queue.run_daily(
        broadcast_top10,
        time=time(hour=TOP10_SEND_HOUR, minute=0, tzinfo=tz),
        name="daily_top10"
    )

# ---------------------------
#  App start
# ---------------------------
def main():
    token = os.environ.get("8452830427:AAE3KBk3pEp7Wblw-JXIacjvAmbcnPp9CZY")
    if not token:
        print("⚠️ BOT_TOKEN kiritilmagan! Iltimos environmentga qo'ying.")
        return

    app = ApplicationBuilder().token(token).build()

    # register handlers
    app.add_handler(CommandHandler("start", start))
    app.add_handler(CommandHandler("help", help_cmd))
    app.add_handler(CommandHandler("profile", profile))
    app.add_handler(CommandHandler("stopdaily", stopdaily))
    app.add_handler(CommandHandler("startdaily", startdaily))
    app.add_handler(CallbackQueryHandler(button_handler))
    app.add_handler(MessageHandler(filters.TEXT & ~filters.COMMAND, text_handler))

    # schedule background jobs
    schedule_jobs(app)

    print("🤖 Bot ishga tushdi. Jobs scheduled.")
    app.run_polling()

if __name__ == "__main__":
    main()
