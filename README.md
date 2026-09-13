# my-first-tg-boе 
import asyncio
import json
import logging
import os
import random
import time
import uuid
from typing import Optional

from aiogram import Bot, Dispatcher, Router, F
from aiogram.client.default import DefaultBotProperties
from aiogram.enums import ParseMode
from aiogram.exceptions import TelegramBadRequest
from aiogram.filters import Command, CommandObject, CommandStart
from aiogram.types import (
    CallbackQuery,
    InlineKeyboardButton,
    InlineKeyboardMarkup,
    Message,
)

logging.basicConfig(level=logging.INFO)
log = logging.getLogger("rps_bot")

BOT_TOKEN = os.getenv("BOT_TOKEN")
DATA_FILE = "data.json"

STARTING_BALANCE = 500
STARTING_RATING = 1000

DAILY_BONUS = 200
DAILY_COOLDOWN_SEC = 24 * 60 * 60

BET_OPTIONS = [25, 50, 100, 250]
START_HP = 3
BOOST_COST = 50
BOOST_HP = 1

RATING_WIN = 25
RATING_LOSE = 15

MOVES = {
    "rock": "🪨 Камень",
    "scissors": "✂️ Ножницы",
    "paper": "📄 Бумага",
}
# ключ побеждает значение
BEATS = {"rock": "scissors", "scissors": "paper", "paper": "rock"}

BOT_USERNAME = ""  # заполняется при старте в main()

router = Router()

# ==================== ХРАНИЛИЩЕ (JSON) ====================

_data_lock = asyncio.Lock()


def _default_user() -> dict:
    return {
        "balance": STARTING_BALANCE,
        "rating": STARTING_RATING,
        "last_daily": 0,
    }


def _load_raw() -> dict:
    if not os.path.exists(DATA_FILE):
        return {}
    try:
        with open(DATA_FILE, "r", encoding="utf-8") as f:
            return json.load(f)
    except (json.JSONDecodeError, OSError):
        log.warning("Не удалось прочитать %s, начинаю с чистого листа", DATA_FILE)
        return {}


def _save_raw(data: dict) -> None:
    with open(DATA_FILE, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=2)


async def get_user(user_id: int) -> dict:
    """Возвращает запись пользователя, создавая её при первом обращении."""
    async with _data_lock:
        data = _load_raw()
        uid = str(user_id)
        if uid not in data:
            data[uid] = _default_user()
            _save_raw(data)
        return data[uid]


async def update_user(user_id: int, **fields) -> dict:
    async with _data_lock:
        data = _load_raw()
        uid = str(user_id)
        if uid not in data:
            data[uid] = _default_user()
        data[uid].update(fields)
        _save_raw(data)
        return data[uid]


async def add_balance(user_id: int, amount: int) -> int:
    async with _data_lock:
        data = _load_raw()
        uid = str(user_id)
        if uid not in data:
            data[uid] = _default_user()
        data[uid]["balance"] = max(0, data[uid]["balance"] + amount)
        _save_raw(data)
        return data[uid]["balance"]


async def add_rating(user_id: int, amount: int) -> int:
    async with _data_lock:
        data = _load_raw()
        uid = str(user_id)
        if uid not in data:
            data[uid] = _default_user()
        data[uid]["rating"] = max(0, data[uid]["rating"] + amount)
        _save_raw(data)
        return data[uid]["rating"]


async def get_top_players(limit: int = 10):
    async with _data_lock:
        data = _load_raw()
    return sorted(data.items(), key=lambda kv: kv[1]["balance"], reverse=True)[:limit]


# ==================== ИГРОВЫЕ СТРУКТУРЫ (В ПАМЯТИ) ====================

class Fighter:
    """Один участник боя — реальный игрок или виртуальный бот-соперник."""

    def __init__(self, user_id: Optional[int], name: str, is_bot: bool = False):
        self.user_id = user_id           # None для ботов-соперников
        self.name = name
        self.is_bot = is_bot
        self.hp = START_HP
        self.max_hp = START_HP
        self.boosted = False             # буст жизни уже использован?
        self.move: Optional[str] = None  # ход в текущем раунде


class Match:
    """Активный матч. mode: 'bot' | 'pvp' | '3way'."""

    def __init__(self, match_id: str, mode: str, bet: int, chat_ids: dict):
        self.match_id = match_id
        self.mode = mode
        self.bet = bet
        self.chat_ids = chat_ids   # user_id -> chat_id, куда слать сообщения
        self.fighters: dict = {}   # ключ ('<user_id>' / 'bot' / 'bot1' / 'bot2') -> Fighter
        self.round_no = 1
        self.finished = False


matches: dict[str, Match] = {}
pending_duels: dict[str, dict] = {}  # code -> {"inviter_id","bet","chat_id","inviter_name"}


def resolve(move_a: str, move_b: str) -> str:
    """Возвращает 'draw', 'a' (a победил) или 'b' (b победил)."""
    if move_a == move_b:
        return "draw"
    return "a" if BEATS[move_a] == move_b else "b"


def hp_bar(hp: int, max_hp: int) -> str:
    return "❤️" * max(0, hp) + "🖤" * max(0, max_hp - hp)


# ==================== КЛАВИАТУРЫ ====================

def main_menu_kb() -> InlineKeyboardMarkup:
    return InlineKeyboardMarkup(inline_keyboard=[
        [InlineKeyboardButton(text="🤖 Игра с Ботом", callback_data="mode:bot")],
        [InlineKeyboardButton(text="⚔️ Дуэль с Другом", callback_data="mode:pvp")],
        [InlineKeyboardButton(text="👥 Замес 1v1v1", callback_data="mode:3way")],
        [InlineKeyboardButton(text="🎁 Ежедневный бонус", callback_data="daily")],
        [InlineKeyboardButton(text="🏆 Топ игроков", callback_data="top")],
    ])


def bet_kb(mode: str) -> InlineKeyboardMarkup:
    row = [InlineKeyboardButton(text=f"{b}$", callback_data=f"bet:{mode}:{b}") for b in BET_OPTIONS]
    return InlineKeyboardMarkup(inline_keyboard=[row, [InlineKeyboardButton(text="⬅️ Назад", callback_data="menu")]])


def move_kb(match_id: str, show_boost: bool) -> InlineKeyboardMarkup:
    rows = [[
        InlineKeyboardButton(text=MOVES["rock"], callback_data=f"move:{match_id}:rock"),
        InlineKeyboardButton(text=MOVES["scissors"], callback_data=f"move:{match_id}:scissors"),
        InlineKeyboardButton(text=MOVES["paper"], callback_data=f"move:{match_id}:paper"),
    ]]
    if show_boost:
        rows.append([InlineKeyboardButton(text=f"🧪 +1 Жизнь (+{BOOST_COST}$)", callback_data=f"boost:{match_id}")])
    return InlineKeyboardMarkup(inline_keyboard=rows)


def back_to_menu_kb() -> InlineKeyboardMarkup:
    return InlineKeyboardMarkup(inline_keyboard=[[InlineKeyboardButton(text="⬅️ В меню", callback_data="menu")]])


# ==================== ТЕКСТ СОСТОЯНИЯ МАТЧА ====================

def build_status_text(match: Match) -> str:
    lines = [f"🎮 Раунд {match.round_no} | Ставка: {match.bet}$\n"]

    if match.mode == "bot":
        uid = list(match.chat_ids.keys())[0]
        p, b = match.fighters[str(uid)], match.fighters["bot"]
        lines.append(f"{p.name}: {hp_bar(p.hp, p.max_hp)}")
        lines.append(f"{b.name}: {hp_bar(b.hp, b.max_hp)}")

    elif match.mode == "pvp":
        for uid in match.chat_ids.keys():
            f = match.fighters[str(uid)]
            lines.append(f"{f.name}: {hp_bar(f.hp, f.max_hp)}")

    elif match.mode == "3way":
        uid = list(match.chat_ids.keys())[0]
        p = match.fighters[str(uid)]
        b1, b2 = match.fighters["bot1"], match.fighters["bot2"]
        lines.append(f"{p.name}: {hp_bar(p.hp, p.max_hp)}")
        lines.append(f"{b1.name}: {hp_bar(b1.hp, b1.max_hp)}")
        lines.append(f"{b2.name}: {hp_bar(b2.hp, b2.max_hp)}")

    lines.append("\nВыбери ход:")
    return "\n".join(lines)


async def render_match(message: Message, match: Match, viewer_id: int, edit: bool = False) -> None:
    """Отправляет/обновляет сообщение с клавиатурой хода для одного игрока."""
    text = build_status_text(match)
    f = match.fighters[str(viewer_id)]
    user = await get_user(viewer_id)
    show_boost = (not f.boosted) and user["balance"] >= BOOST_COST
    kb = move_kb(match.match_id, show_boost)
    if edit:
        try:
            await message.edit_text(text, reply_markup=kb)
            return
        except TelegramBadRequest:
            pass
    await message.answer(text, reply_markup=kb)


# ==================== ГЛАВНОЕ МЕНЮ / СЕРВИСНЫЕ КОМАНДЫ ====================

@router.message(CommandStart())
async def cmd_start(message: Message, command: CommandObject) -> None:
    user = await get_user(message.from_user.id)
    payload = command.args

    # Переход по реферальной ссылке дуэли: /start duel_<code>
    if payload and payload.startswith("duel_"):
        code = payload[len("duel_"):]
        await handle_duel_join(message, code)
        return

    await message.answer(
        f"👋 Привет, {message.from_user.first_name}!\n\n"
        f"Это бот для игры в «Камень, Ножницы, Бумага» (Су-Ли-Фа).\n"
        f"💰 Баланс: {user['balance']}$\n"
        f"🏆 Рейтинг: {user['rating']}\n\n"
        f"Выбери режим игры:",
        reply_markup=main_menu_kb(),
    )


@router.callback_query(F.data == "menu")
async def cb_menu(call: CallbackQuery) -> None:
    user = await get_user(call.from_user.id)
    await call.message.edit_text(
        f"💰 Баланс: {user['balance']}$\n🏆 Рейтинг: {user['rating']}\n\nВыбери режим игры:",
        reply_markup=main_menu_kb(),
    )
    await call.answer()


@router.callback_query(F.data == "daily")
async def cb_daily(call: CallbackQuery) -> None:
    user = await get_user(call.from_user.id)
    now = time.time()
    remaining = DAILY_COOLDOWN_SEC - (now - user["last_daily"])

    if remaining > 0:
        h, rem = divmod(int(remaining), 3600)
        m = rem // 60
        await call.answer(f"⏳ Бонус будет доступен через {h} ч {m} мин.", show_alert=True)
        return

    await update_user(call.from_user.id, last_daily=now)
    new_balance = await add_balance(call.from_user.id, DAILY_BONUS)
    await call.answer(f"🎁 Вы получили {DAILY_BONUS}$! Баланс: {new_balance}$", show_alert=True)


@router.callback_query(F.data == "top")
async def cb_top(call: CallbackQuery, bot: Bot) -> None:
    top = await get_top_players(10)
    lines = ["🏆 Топ игроков по балансу:\n"]
    for i, (uid, info) in enumerate(top, start=1):
        name = uid
        try:
            chat = await bot.get_chat(int(uid))
            name = chat.first_name or chat.username or uid
        except Exception:
            pass
        lines.append(f"{i}. {name} — {info['balance']}$ (рейтинг {info['rating']})")

    if len(lines) == 1:
        lines.append("Пока никто не сыграл.")

    await call.message.edit_text("\n".join(lines), reply_markup=back_to_menu_kb())
    await call.answer()


@router.callback_query(F.data.startswith("mode:"))
async def cb_mode(call: CallbackQuery) -> None:
    mode = call.data.split(":")[1]
    titles = {
        "bot": "🤖 Игра с ботом",
        "pvp": "⚔️ Дуэль с другом",
        "3way": "👥 Замес 1v1v1",
    }
    await call.message.edit_text(f"{titles[mode]}\n\nВыберите ставку:", reply_markup=bet_kb(mode))
    await call.answer()


@router.callback_query(F.data.startswith("bet:"))
async def cb_bet(call: CallbackQuery, bot: Bot) -> None:
    _, mode, bet_str = call.data.split(":")
    bet = int(bet_str)
    user = await get_user(call.from_user.id)

    if user["balance"] < bet:
        await call.answer("❌ Недостаточно средств для этой ставки!", show_alert=True)
        return

    if mode == "bot":
        await start_bot_match(call, bet)
    elif mode == "3way":
        await start_3way_match(call, bet)
    elif mode == "pvp":
        await create_duel_invite(call, bet)

    await call.answer()


# ==================== РЕЖИМ: ИГРА С БОТОМ ====================

async def start_bot_match(call: CallbackQuery, bet: int) -> None:
    user_id = call.from_user.id
    await add_balance(user_id, -bet)

    match_id = str(uuid.uuid4())[:8]
    match = Match(match_id, "bot", bet, {user_id: call.message.chat.id})
    match.fighters[str(user_id)] = Fighter(user_id, call.from_user.first_name)
    match.fighters["bot"] = Fighter(None, "Бот 🤖", is_bot=True)
    matches[match_id] = match

    await render_match(call.message, match, user_id, edit=True)


async def process_bot_round(message: Message, match: Match, bot: Bot) -> None:
    uid = list(match.chat_ids.keys())[0]
    p, b = match.fighters[str(uid)], match.fighters["bot"]
    b.move = random.choice(list(MOVES.keys()))

    outcome = resolve(p.move, b.move)
    result = f"Вы: {MOVES[p.move]}  vs  Бот: {MOVES[b.move]}\n"
    if outcome == "draw":
        result += "🤝 Ничья! HP не меняется."
    elif outcome == "a":
        b.hp -= 1
        result += "✅ Вы выиграли раунд!"
    else:
        p.hp -= 1
        result += "❌ Вы проиграли раунд!"

    p.move, b.move = None, None
    match.round_no += 1

    if p.hp <= 0 or b.hp <= 0:
        await finish_solo_match(message, match, result)
        return

    text = result + "\n\n" + build_status_text(match)
    show_boost = (not p.boosted) and (await get_user(uid))["balance"] >= BOOST_COST
    await message.edit_text(text, reply_markup=move_kb(match.match_id, show_boost))


# ==================== РЕЖИМ: 1v1v1 ====================

async def start_3way_match(call: CallbackQuery, bet: int) -> None:
    user_id = call.from_user.id
    await add_balance(user_id, -bet)

    match_id = str(uuid.uuid4())[:8]
    match = Match(match_id, "3way", bet, {user_id: call.message.chat.id})
    match.fighters[str(user_id)] = Fighter(user_id, call.from_user.first_name)
    match.fighters["bot1"] = Fighter(None, "Бот-1 🤖", is_bot=True)
    match.fighters["bot2"] = Fighter(None, "Бот-2 🤖", is_bot=True)
    matches[match_id] = match

    await render_match(call.message, match, user_id, edit=True)


async def process_3way_round(message: Message, match: Match, bot: Bot) -> None:
    uid = list(match.chat_ids.keys())[0]
    p = match.fighters[str(uid)]
    b1, b2 = match.fighters["bot1"], match.fighters["bot2"]

    lines = [f"Вы: {MOVES[p.move]}"]
    for b in (b1, b2):
        if b.hp <= 0:
            continue  # этот бот уже выбыл из боя
        b.move = random.choice(list(MOVES.keys()))
        outcome = resolve(p.move, b.move)
        lines.append(f"{b.name} ходит: {MOVES[b.move]}")
        if outcome == "draw":
            lines.append(f"🤝 Ничья с {b.name}.")
        elif outcome == "a":
            b.hp -= 1
            lines.append(f"✅ Вы побеждаете {b.name} в этом раунде!")
        else:
            p.hp -= 1
            lines.append(f"❌ {b.name} побеждает вас в этом раунде!")

    p.move, b1.move, b2.move = None, None, None
    match.round_no += 1

    if p.hp <= 0 or (b1.hp <= 0 and b2.hp <= 0):
        await finish_solo_match(message, match, "\n".join(lines))
        return

    text = "\n".join(lines) + "\n\n" + build_status_text(match)
    show_boost = (not p.boosted) and (await get_user(uid))["balance"] >= BOOST_COST
    await message.edit_text(text, reply_markup=move_kb(match.match_id, show_boost))


# ==================== ЗАВЕРШЕНИЕ СОЛО-МАТЧЕЙ (bot / 3way) ====================

async def finish_solo_match(message: Message, match: Match, last_result: str) -> None:
    match.finished = True
    uid = list(match.chat_ids.keys())[0]
    p = match.fighters[str(uid)]
    player_won = p.hp > 0

    # В режиме 1v1v1 ставка игрока условно перекрывает двух ботов сразу,
    # поэтому выигрыш больше, чем в дуэли один-на-один с ботом.
    payout_multiplier = 3 if match.mode == "3way" else 2

    if player_won:
        payout = match.bet * payout_multiplier
        new_balance = await add_balance(uid, payout)
        await add_rating(uid, RATING_WIN)
        outcome_text = f"\n\n🏆 Победа! Вы получаете {payout}$.\n💰 Баланс: {new_balance}$"
    else:
        await add_rating(uid, -RATING_LOSE)
        user = await get_user(uid)
        outcome_text = f"\n\n💀 Поражение! Ставка {match.bet}$ сгорает.\n💰 Баланс: {user['balance']}$"

    text = last_result + outcome_text
    try:
        await message.edit_text(text, reply_markup=back_to_menu_kb())
    except TelegramBadRequest:
        await message.answer(text, reply_markup=back_to_menu_kb())

    matches.pop(match.match_id, None)


# ==================== РЕЖИМ: ДУЭЛЬ (PvP ПО ССЫЛКЕ) ====================

async def create_duel_invite(call: CallbackQuery, bet: int) -> None:
    user_id = call.from_user.id
    code = str(uuid.uuid4())[:8]
    pending_duels[code] = {
        "inviter_id": user_id,
        "inviter_name": call.from_user.first_name,
        "bet": bet,
        "chat_id": call.message.chat.id,
    }

    link = f"https://t.me/{BOT_USERNAME}?start=duel_{code}"
    text = (
        f"⚔️ Дуэль создана! Ставка: {bet}$\n\n"
        f"Отправь эту ссылку другу, чтобы начать матч:\n{link}\n\n"
        f"Ссылка одноразовая и активна, пока по ней кто-то не перейдёт."
    )
    await call.message.edit_text(text, reply_markup=back_to_menu_kb())


async def handle_duel_join(message: Message, code: str) -> None:
    duel = pending_duels.get(code)
    if not duel:
        await message.answer(
            "❌ Эта дуэльная ссылка недействительна или уже была использована.",
            reply_markup=main_menu_kb(),
        )
        return

    inviter_id = duel["inviter_id"]
    joiner_id = message.from_user.id

    if joiner_id == inviter_id:
        await message.answer("Нельзя сражаться самим с собой 🙂", reply_markup=main_menu_kb())
        return

    bet = duel["bet"]
    joiner = await get_user(joiner_id)
    inviter = await get_user(inviter_id)

    if joiner["balance"] < bet:
        await message.answer("❌ У вас недостаточно средств для этой ставки.", reply_markup=main_menu_kb())
        return
    if inviter["balance"] < bet:
        pending_duels.pop(code, None)
        await message.answer(
            "❌ У создателя дуэли больше нет средств на эту ставку. Дуэль отменена.",
            reply_markup=main_menu_kb(),
        )
        return

    del pending_duels[code]
    await add_balance(inviter_id, -bet)
    await add_balance(joiner_id, -bet)

    match_id = str(uuid.uuid4())[:8]
    match = Match(
        match_id,
        "pvp",
        bet,
        {inviter_id: duel["chat_id"], joiner_id: message.chat.id},
    )
    match.fighters[str(inviter_id)] = Fighter(inviter_id, duel["inviter_name"])
    match.fighters[str(joiner_id)] = Fighter(joiner_id, message.from_user.first_name)
    matches[match_id] = match

    for uid in (inviter_id, joiner_id):
        user = await get_user(uid)
        show_boost = user["balance"] >= BOOST_COST
        text = f"⚔️ Дуэль началась! Ставка: {bet}$\n\n" + build_status_text(match)
        try:
            await message.bot.send_message(
                match.chat_ids[uid], text, reply_markup=move_kb(match_id, show_boost)
            )
        except TelegramBadRequest:
            pass


async def process_pvp_move(call: CallbackQuery, match: Match, bot: Bot) -> None:
    ids = list(match.chat_ids.keys())
    id_a, id_b = ids[0], ids[1]
    fa, fb = match.fighters[str(id_a)], match.fighters[str(id_b)]

    if fa.move is None or fb.move is None:
        # Ход сделал только один игрок — уведомляем второго, результат скрыт
        mover_id = call.from_user.id
        other_id = id_b if mover_id == id_a else id_a
        try:
            await bot.send_message(match.chat_ids[other_id], "⚡ Соперник уже сходил! Твоя очередь...")
        except TelegramBadRequest:
            pass
        return

    # Оба сходили — раскрываем результат раунда
    outcome = resolve(fa.move, fb.move)
    result = f"{fa.name}: {MOVES[fa.move]}\n{fb.name}: {MOVES[fb.move]}\n\n"
    if outcome == "draw":
        result += "🤝 Ничья! HP не меняется."
    elif outcome == "a":
        fb.hp -= 1
        result += f"✅ {fa.name} выигрывает раунд!"
    else:
        fa.hp -= 1
        result += f"✅ {fb.name} выигрывает раунд!"

    fa.move, fb.move = None, None
    match.round_no += 1

    if fa.hp <= 0 or fb.hp <= 0:
        await finish_pvp_match(match, bot, result)
        return

    for uid in ids:
        f = match.fighters[str(uid)]
        user = await get_user(uid)
        show_boost = (not f.boosted) and user["balance"] >= BOOST_COST
        text = result + "\n\n" + build_status_text(match)
        try:
            await bot.send_message(match.chat_ids[uid], text, reply_markup=move_kb(match.match_id, show_boost))
        except TelegramBadRequest:
            pass


async def finish_pvp_match(match: Match, bot: Bot, last_result: str) -> None:
    match.finished = True
    ids = list(match.chat_ids.keys())
    id_a, id_b = ids[0], ids[1]
    fa, fb = match.fighters[str(id_a)], match.fighters[str(id_b)]
    pot = match.bet * 2

    if fa.hp <= 0 and fb.hp <= 0:
        # Оба выбыли одновременно (например, у обоих закончился буст в один момент)
        await add_balance(id_a, match.bet)
        await add_balance(id_b, match.bet)
        summary_a = summary_b = "\n\n🤝 Ничья! Ставки возвращены обоим игрокам."
    elif fa.hp <= 0:
        new_b = await add_balance(id_b, pot)
        await add_rating(id_b, RATING_WIN)
        await add_rating(id_a, -RATING_LOSE)
        summary_a = f"\n\n💀 Вы проиграли матч. Ставка {match.bet}$ потеряна."
        summary_b = f"\n\n🏆 Вы выиграли матч! +{pot}$. Баланс: {new_b}$"
    else:
        new_a = await add_balance(id_a, pot)
        await add_rating(id_a, RATING_WIN)
        await add_rating(id_b, -RATING_LOSE)
        summary_b = f"\n\n💀 Вы проиграли матч. Ставка {match.bet}$ потеряна."
        summary_a = f"\n\n🏆 Вы выиграли матч! +{pot}$. Баланс: {new_a}$"

    for uid, summary in ((id_a, summary_a), (id_b, summary_b)):
        try:
            await bot.send_message(match.chat_ids[uid], last_result + summary, reply_markup=back_to_menu_kb())
        except TelegramBadRequest:
            pass

    matches.pop(match.match_id, None)


# ==================== ОБЩИЕ ОБРАБОТЧИКИ ХОДА И БУСТА ====================

@router.callback_query(F.data.startswith("move:"))
async def cb_move(call: CallbackQuery, bot: Bot) -> None:
    _, match_id, move = call.data.split(":")
    match = matches.get(match_id)

    if not match or match.finished:
        await call.answer("Этот матч уже завершён.", show_alert=True)
        return

    uid = call.from_user.id
    f = match.fighters.get(str(uid))
    if not f:
        await call.answer("Вы не участвуете в этом матче.", show_alert=True)
        return
    if f.move is not None:
        await call.answer("Вы уже сделали ход в этом раунде, ждите соперника.", show_alert=True)
        return

    f.move = move
    await call.answer(f"Ваш ход: {MOVES[move]}")

    if match.mode == "bot":
        await process_bot_round(call.message, match, bot)
    elif match.mode == "3way":
        await process_3way_round(call.message, match, bot)
    elif match.mode == "pvp":
        await process_pvp_move(call, match, bot)


@router.callback_query(F.data.startswith("boost:"))
async def cb_boost(call: CallbackQuery) -> None:
    _, match_id = call.data.split(":")
    match = matches.get(match_id)

    if not match or match.finished:
        await call.answer("Матч уже завершён.", show_alert=True)
        return

    uid = call.from_user.id
    f = match.fighters.get(str(uid))
    if not f:
        await call.answer("Вы не участвуете в этом матче.", show_alert=True)
        return
    if f.boosted:
        await call.answer("Вы уже использовали буст в этом матче.", show_alert=True)
        return

    user = await get_user(uid)
    if user["balance"] < BOOST_COST:
        await call.answer("Недостаточно средств для буста.", show_alert=True)
        return

    await add_balance(uid, -BOOST_COST)
    f.boosted = True
    f.hp += BOOST_HP
    f.max_hp += BOOST_HP

    await call.answer(f"🧪 +1 Жизнь! Текущее HP: {f.hp}")
    await render_match(call.message, match, uid, edit=True)


# ==================== ЧИТ-КОД ====================

@router.message(Command("cheat"))
async def cmd_cheat(message: Message, command: CommandObject) -> None:
    if command.args and command.args.strip() == "777":
        new_balance = await add_balance(message.from_user.id, 10_000)
        await message.answer(f"🎉 Чит-код активирован! +10 000$\n💰 Баланс: {new_balance}$")
    else:
        await message.answer("Неверный чит-код.")


@router.message(F.text == "777")
async def msg_cheat(message: Message) -> None:
    new_balance = await add_balance(message.from_user.id, 10_000)
    await message.answer(f"🎉 Чит-код активирован! +10 000$\n💰 Баланс: {new_balance}$")


# ==================== ТОЧКА ВХОДА ====================

async def main() -> None:
    global BOT_USERNAME

    if not BOT_TOKEN or BOT_TOKEN == "PUT_YOUR_TOKEN_HERE":
        raise RuntimeError(
           
        )

    bot = Bot(token=BOT_TOKEN, default=DefaultBotProperties(parse_mode=ParseMode.HTML))
    dp = Dispatcher()
    dp.include_router(router)

    me = await bot.get_me()
    BOT_USERNAME = me.username
    log.info("Бот запущен: @%s", BOT_USERNAME)

    await dp.start_polling(bot)


if __name__ == "__main__":
    asyncio.run(main())
