# pizza_administrationBot
From the bot you can offer pizza
    from aiogram import Dispatcher, executor, types, Bot
    from aiogram.dispatcher.filters.state import State, StatesGroup
    from aiogram.contrib.fsm_storage.memory import MemoryStorage
    from aiogram.dispatcher import FSMContext
    from aiogram.types import KeyboardButton, ReplyKeyboardMarkup
    
    Type = ReplyKeyboardMarkup(
        keyboard=[
            [
                KeyboardButton("🍕Sicilian pizza🍕"),
                KeyboardButton("🍕New York–style pizza🍕"),
                KeyboardButton('🍕Calzone🍕')
            ],
            [
                KeyboardButton("🍕Chicago-style pizza🍕"),
                KeyboardButton("🍕Pizza Margherita🍕"),
                KeyboardButton("🍕Detroit-style pizza🍕")
            ],
            [
                KeyboardButton("🍕Neapolitan pizza🍕"),
                KeyboardButton("🍕California-style pizza🍕"),
                KeyboardButton('🍕Stromboli🍕')
            ]
        ], resize_keyboard=True
    )
    
    pizza_size = ReplyKeyboardMarkup(
        keyboard=[
            [
    
                KeyboardButton('Small🤏')
            ],
            [
                KeyboardButton('Medium🙌')
            ],
            [
                KeyboardButton('Huge👐')
            ]
        ], resize_keyboard=True
    )
    loc = ReplyKeyboardMarkup(
        keyboard=[
            [
                KeyboardButton('Location📍', request_location=True)
            ]
        ], resize_keyboard=True
    )
    # 1️⃣2️⃣3️⃣4️⃣5️⃣6️⃣7️⃣8️⃣9️⃣🔟
    num = ReplyKeyboardMarkup(
        keyboard=[
            [
                KeyboardButton('1️⃣'),
                KeyboardButton('2️⃣'),
                KeyboardButton('3️⃣'),
                KeyboardButton('4️⃣'),
                KeyboardButton('5️⃣')
            ],
            [
                KeyboardButton('6️⃣'),
                KeyboardButton('7️⃣'),
                KeyboardButton('8️⃣'),
                KeyboardButton('9️⃣'),
                KeyboardButton('🔟')
            ]
        ], resize_keyboard=True
    )
    
    ask = ReplyKeyboardMarkup(
        keyboard=[
            [
                KeyboardButton('Yes✔️'),
                KeyboardButton('No❌')
            ]
        ], resize_keyboard=True
    )
    start = ReplyKeyboardMarkup(
        keyboard=[
            [
                KeyboardButton('/start')
            ]
        ], resize_keyboard=True
    )
    
    class Offer_data(StatesGroup):
        Piz_type = State()
        Piz_size = State()
        Piz_num = State()
        User_d = State()
        offer_d = State()
    
    
    bot = Bot(token='6616002380:AAEYXoPghSiaJImI67c9_P9BT9S0V0rYdh4')
    storage = MemoryStorage()
    dp = Dispatcher(bot=bot, storage=storage)
    
    
    @dp.message_handler(text='/start')
    async def cmd_start(message: types.Message):
        await Offer_data.Piz_type.set()
        await message.answer(f'👋Hi {message.from_user.full_name}\n'
                             f'Welcome to pizzateria🫱\n'
                             f'🍕first, choose pizza to offer👇', reply_markup=Type)
        await Offer_data.next()
    
    
    @dp.message_handler(state=Offer_data.Piz_size)
    async def p_name(message: types.Message, state: FSMContext):
        async with state.proxy() as data:
            data['p_name'] = message.text
        await message.answer('Accepted👍\n'
                             'Now,choose size of the pizza🔬🔭', reply_markup=pizza_size)
        await Offer_data.next()
    
    
    @dp.message_handler(state=Offer_data.Piz_num)
    async def p_num(message: types.Message, state: FSMContext):
        async with state.proxy() as data:
            data['size'] = message.text
        await message.answer('Accepted👍\n'
                             'Finally, how many pizzas do u offer?', reply_markup=num)
        await Offer_data.next()
    
    
    @dp.message_handler(state=Offer_data.User_d)
    async def u_data(message: types.Message, state: FSMContext):
        async with state.proxy() as data:
            data['pi_num'] = message.text
        await message.answer('Accepted✔️\n'
                             '✍️Your order:\n'
                             f'     name:{data["p_name"]}\n'
                             f'     size:{data["size"]}\n'
                             f'     the number:{data["pi_num"]}\n'
                             f'Do u offer❔', reply_markup=ask)
        await Offer_data.next()
    
    
    @dp.message_handler(state=Offer_data.offer_d)
    async def cmd_asking(message: types.Message, state: FSMContext):
        async with state.proxy() as data:
            data['message'] = message.text
        d = data["message"]
        # print(len(d))
        if len(d) == 4:
            await message.answer("📍Click it to give your location👇\n", reply_markup=loc)
            await message.answer("💸In cash or  \n"
                                 "💳Click(6348 3648 6483 2384)\n"
                                 "Courier🛵 will come🚀 in 15minutes⏱")
            await bot.send_message(
                chat_id=5027080397,
                text=f'from: {message.from_user.full_name}\n'
                     f'user_n: {message.from_user.username}\n'
                     f'nomi:{data["p_name"]}\n'
                     f'o`lchami:{data["size"]}\n'
                     f'soni:{data["pi_num"]}\n'
            )
        else:
            await message.answer('❌🙅‍♂Your order rejected🙅‍♀❌', reply_markup=start)
        await state.finish()
