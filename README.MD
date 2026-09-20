# 🌫️ Telefog

کتابخانه‌ی 
**Telegram Bot API**
 برای زبان برنامه‌نویسی 
**Lucy**.
تمام درخواست‌ها با `curl` و هدر  `Content-Type: application/json` ارسال می‌شوند.

البته در آینده با lib http نیز  این عمل صورت خواهد گرفت در هر صورت تداخلی در lib ما ایجاد نخواهد شد
---

## فهرست

1. [ویژگی‌ها](#-ویژگی‌ها)
2. [پیش‌نیازها و نصب curl](#-پیش‌نیازها-و-نصب-curl)
3. [شروع سریع](#-شروع-سریع)
4. [ساخت ربات با Telefog](#-ساخت-ربات-با-telefog)
5. [مرجع متدها](#-مرجع-متدها)
6. [سازنده‌های دکمه و کیبورد](#-سازنده‌های-دکمه-و-کیبورد)
7. [مثال کامل](#-مثال-کامل)
8. [پروکسی](#-پروکسی)
9. [دیباگ و رفع خطا](#-دیباگ-و-رفع-خطا)
10. [نکات مهم](#-نکات-مهم)

---

## ✨ ویژگی‌ها

- ارسال پیام، عکس، تاس، فوروارد و کپی پیام
- ویرایش و حذف پیام
- دکمه‌ی شیشه‌ای (Inline)، کیبورد معمولی (Reply) و حذف کیبورد
- پاسخ به Callback Query
- تنظیم دستورات ربات (`/start`، `/ping` و …)
- Long Polling با `get_updates` و مدیریت Webhook
- `call_api` برای صدا زدن هر متد دلخواه Bot API
- پشتیبانی از فارسی و ایموجی
- چاپ خودکار خطاها و حالت `debug`

---

## 🔧 پیش‌نیازها و نصب curl

Telefog برای ارسال درخواست از `curl` استفاده می‌کند. اگر `curl` نصب نباشد، به‌طور خودکار به `http.post` برمی‌گردد؛ اما `http.post` هدر JSON نمی‌فرستد و تلگرام بدنه را نمی‌خواند، پس **حتماً curl را نصب کنید**.

بررسی نصب بودن:

```bash
curl --version
```

### Debian / Ubuntu / Linux Mint / Kali / Pop!_OS

```bash
sudo apt update
sudo apt install -y curl
```

### Fedora

```bash
sudo dnf install -y curl
```

### RHEL / CentOS / Rocky / AlmaLinux

```bash
sudo dnf install -y curl
# در نسخه‌های قدیمی‌تر:
sudo yum install -y curl
```

### Arch Linux / Manjaro / EndeavourOS

```bash
sudo pacman -Sy --noconfirm curl
```

### openSUSE / SLES

```bash
sudo zypper install -y curl
```

### Alpine Linux

```bash
sudo apk add --no-cache curl
```

### Void Linux

```bash
sudo xbps-install -Sy curl
```

### Gentoo

```bash
sudo emerge --ask net-misc/curl
```

### NixOS / Nix

```bash
nix-env -iA nixpkgs.curl
# یا موقت:
nix-shell -p curl
```

### Termux (اندروید)

```bash
pkg update
pkg install -y curl
```

### FreeBSD

```bash
sudo pkg install -y curl
```

### macOS

```bash
brew install curl
```

### Windows

`curl.exe` در ویندوز ۱۰ و ۱۱ به‌صورت پیش‌فرض وجود دارد. در غیر این صورت:

```powershell
winget install cURL.cURL
# یا با Chocolatey:
choco install curl -y
```

---

## 🚀 شروع سریع

فایل `telefog.lucy` را کنار فایل ربات خود قرار دهید:

```text
my-bot/
├── telefog.lucy
└── bot.lucy
```
## LPM

```LPM
lpm install --lpm telefog-1.0.0.plm
```

```text
my-bot/
└── bot.lucy
```

```lucy
from telefog import Telefog

bot = Telefog.new("توکن_ربات_شما")
bot.send_message(123456789, "سلام از Lucy! 👋")
```

اجرا:

```bash
lucy bot.lucy
```

> ⚠️ **توکن ربات را جایی منتشر نکنید.** اگر لو رفت، در @BotFather دستور `/revoke` را بزنید.

---

## 🤖 ساخت ربات با Telefog

1. در تلگرام به @BotFather پیام بدهید و `/newbot` را بزنید.
2. نام و username ربات را بدهید و **توکن** را بگیرید.
3. برای دریافت `chat_id` خودتان، به ربات پیام بدهید و مقدار `msg["chat"]["id"]` را از `get_updates` بخوانید.

---

## 📚 مرجع متدها

همه‌ی متدها نتیجه‌ی پاسخ تلگرام را به‌صورت map برمی‌گردانند:

```lucy
res = bot.send_message(chat_id, "سلام")
if res != nil and res["ok"] == true
    print "ارسال شد. شناسه پیام: " + res["result"]["message_id"]
end
```

- اگر خطای شبکه رخ دهد، مقدار `nil` برمی‌گردد.
- اگر تلگرام خطا بدهد، پیام خطا خودکار چاپ می‌شود و `res["ok"]` برابر `false` است.

### `Telefog.new(token)`

ساخت یک نمونه‌ی ربات.

```lucy
bot = Telefog.new("123456:ABC-DEF...")
```

### `bot.debug`

با `true` کردن آن، بدنه‌ی تمام درخواست‌ها چاپ می‌شود.

```lucy
bot.debug = true
```

---

### `call_api(method_name, payload = nil)`

صدا زدن هر متد Bot API. `payload` می‌تواند map یا یک رشته‌ی JSON آماده باشد.

```lucy
res = bot.call_api("getMe")
print res["result"]["username"]
```

```lucy
# با payload به‌صورت رشته‌ی JSON
res = bot.call_api("getChat", "{\"chat_id\":123456789}")
```

---

### `get_updates(offset = 0, timeout = 30, limit = 100, allowed_updates = nil)`

دریافت پیام‌های جدید با Long Polling.

| پارامتر | توضیح |
|---|---|
| `offset` | شناسه‌ی آپدیت بعدی (`update_id + 1`) |
| `timeout` | مدت انتظار برای پیام جدید (ثانیه) |
| `limit` | حداکثر تعداد آپدیت در هر بار (۱ تا ۱۰۰) |
| `allowed_updates` | آرایه‌ای از نوع آپدیت‌های مجاز |

```lucy
offset = 0
while true
    updates = bot.get_updates(offset, 10, 100)
    if updates != nil and updates["ok"] == true
        for update in updates["result"]
            offset = update["update_id"] + 1
            print update
        end
    end
end
```

فقط پیام و کلیک روی دکمه:

```lucy
updates = bot.get_updates(offset, 10, 100, ["message", "callback_query"])
```

---

### `send_message(chat_id, text, parse_mode = "HTML", reply_to_message_id = nil, reply_markup = nil, disable_web_page_preview = false)`

ارسال پیام متنی.

```lucy
# ساده
bot.send_message(chat_id, "سلام!")

# متن غنی با HTML
bot.send_message(chat_id, "<b>پررنگ</b> و <i>کج</i> و <code>کد</code>")

# ریپلای روی یک پیام
bot.send_message(chat_id, "این جواب توست", "HTML", msg_id)

# همراه با دکمه
markup = bot.inline_keyboard([[bot.button_cb("کلیک کن", "clicked")]])
bot.send_message(chat_id, "یک گزینه را انتخاب کنید:", "HTML", nil, markup)

# بدون پیش‌نمایش لینک
bot.send_message(chat_id, "https://github.com", "HTML", nil, nil, true)

# بدون parse_mode (متن خام)
bot.send_message(chat_id, "1 < 2 & 3 > 2", "")
```

---

### `forward_message(chat_id, from_chat_id, message_id, protect_content = false)`

فوروارد یک پیام (با نام فرستنده‌ی اصلی).

```lucy
bot.forward_message(target_chat, source_chat, 42)

# جلوگیری از ذخیره و فوروارد مجدد
bot.forward_message(target_chat, source_chat, 42, true)
```

---

### `copy_message(chat_id, from_chat_id, message_id, caption = "", parse_mode = "HTML", reply_markup = nil)`

کپی یک پیام (بدون نام فرستنده‌ی اصلی).

```lucy
bot.copy_message(target_chat, source_chat, 42)

# با کپشن جدید
bot.copy_message(target_chat, source_chat, 42, "<b>کپشن تازه</b>")
```

---

### `send_photo(chat_id, photo, caption = "", parse_mode = "HTML", reply_to_message_id = nil, reply_markup = nil)`

ارسال عکس. `photo` باید **لینک عمومی (URL)** یا **file_id** باشد. آپلود مستقیم فایل محلی پشتیبانی نمی‌شود.

```lucy
bot.send_photo(chat_id, "https://example.com/cat.jpg")

bot.send_photo(chat_id, "https://example.com/cat.jpg", "یک <b>گربه</b> 🐱")

# با file_id عکسی که قبلاً در تلگرام آپلود شده
bot.send_photo(chat_id, "AgACAgQAAxkBAAI...")
```

---

### `send_dice(chat_id, emoji = "🎲")`

ارسال انیمیشن تاس. ایموجی‌های مجاز: 🎲 🎯 🏀 ⚽ 🎳 🎰

```lucy
bot.send_dice(chat_id)
bot.send_dice(chat_id, "🎯")
bot.send_dice(chat_id, "🎰")
```

---

### `send_chat_action(chat_id, action)`

نمایش وضعیت «در حال تایپ…» و مانند آن. حدود ۵ ثانیه نمایش داده می‌شود.

مقادیر `action`:
`typing`، `upload_photo`، `record_video`، `upload_video`، `record_voice`، `upload_voice`، `upload_document`، `choose_sticker`، `find_location`، `record_video_note`، `upload_video_note`

```lucy
bot.send_chat_action(chat_id, "typing")
sleep(1500)
bot.send_message(chat_id, "جواب آماده شد!")
```

---

### `edit_message_text(chat_id, message_id, text, parse_mode = "HTML", reply_markup = nil)`

ویرایش متن پیامی که ربات فرستاده است.

```lucy
res = bot.send_message(chat_id, "در حال پردازش...")
sleep(2000)
bot.edit_message_text(chat_id, res["result"]["message_id"], "✅ <b>تمام شد!</b>")
```

---

### `delete_message(chat_id, message_id)`

حذف یک پیام.

```lucy
bot.delete_message(chat_id, msg_id)
```

---

### `get_chat(chat_id)`

اطلاعات یک چت، گروه یا کانال.

```lucy
info = bot.get_chat(chat_id)
if info != nil and info["ok"] == true
    print info["result"]["type"]
end
```

---

### `get_chat_member_count(chat_id)`

تعداد اعضای گروه یا کانال.

```lucy
res = bot.get_chat_member_count("@my_channel")
print "تعداد اعضا: " + res["result"]
```

---

### `answer_callback_query(callback_query_id, text = "", show_alert = false, url = "", cache_time = 0)`

پاسخ به کلیک روی دکمه‌ی شیشه‌ای. **حتماً** باید صدا زده شود، وگرنه دکمه در تلگرام همچنان در حال بارگذاری می‌ماند.

```lucy
# اعلان کوچک بالای صفحه
bot.answer_callback_query(cb_id, "ثبت شد ✅")

# پنجره‌ی هشدار (با دکمه‌ی OK)
bot.answer_callback_query(cb_id, "این یک هشدار است!", true)

# بدون متن
bot.answer_callback_query(cb_id)
```

---

### `set_my_commands(commands, scope = nil, language_code = "")`

تنظیم منوی دستورات ربات (دکمه‌ی `/` در تلگرام).

```lucy
cmd_start = {command: "start", description: "شروع کار با ربات"}
cmd_ping = {command: "ping", description: "تست وضعیت اتصال"}
bot.set_my_commands([cmd_start, cmd_ping])
```

با زبان مشخص:

```lucy
bot.set_my_commands([cmd_start], nil, "fa")
```

---

### `set_webhook(url, secret_token = "", max_connections = 40)`

```lucy
bot.set_webhook("https://example.com/hook")
bot.set_webhook("https://example.com/hook", "my_secret_123", 20)
```

> آدرس باید **HTTPS** و از بیرون در دسترس باشد. Telefog سرور Webhook ندارد؛ فقط ثبتش می‌کند.

### `delete_webhook(drop_pending_updates = false)`

حذف Webhook (لازم است اگر می‌خواهید از `get_updates` استفاده کنید).

```lucy
bot.delete_webhook()
bot.delete_webhook(true)   # پیام‌های در صف هم پاک شوند
```

### `get_webhook_info()`

```lucy
info = bot.get_webhook_info()
print info["result"]["url"]
```

---

## ⌨️ سازنده‌های دکمه و کیبورد

### `button_cb(text, callback_data)`

دکمه‌ای که با کلیک، `callback_query` می‌فرستد.

```lucy
btn = bot.button_cb("🎲 تاس بنداز", "cmd_dice")
```

### `button_url(text, url)`

دکمه‌ای که لینک را باز می‌کند.

```lucy
btn = bot.button_url("🌐 گیتهاب", "https://github.com")
```

### `button_reply(text)`

دکمه‌ی کیبورد معمولی (وقتی کلیک شود، متنش به‌عنوان پیام فرستاده می‌شود).

```lucy
btn = bot.button_reply("📞 تماس با ما")
```

### `inline_keyboard(buttons_matrix)`

کیبورد شیشه‌ای زیر پیام. آرایه‌ای از **ردیف‌ها**؛ هر ردیف آرایه‌ای از دکمه‌ها.

```lucy
row1 = [bot.button_cb("✅ بله", "yes"), bot.button_cb("❌ خیر", "no")]
row2 = [bot.button_url("🌐 وبسایت", "https://example.com")]
markup = bot.inline_keyboard([row1, row2])

bot.send_message(chat_id, "موافقید؟", "HTML", nil, markup)
```

### `reply_keyboard(buttons_matrix, resize = true, one_time = false, input_field_placeholder = "")`

کیبورد معمولی جایگزین کیبورد گوشی.

```lucy
row1 = [bot.button_reply("🍕 پیتزا"), bot.button_reply("🍔 برگر")]
row2 = [bot.button_reply("🔙 بازگشت")]
kb = bot.reply_keyboard([row1, row2], true, false, "یکی را انتخاب کن...")

bot.send_message(chat_id, "چی می‌خوری؟", "HTML", nil, kb)
```

### `remove_keyboard(selective = false)`

حذف کیبورد معمولی.

```lucy
bot.send_message(chat_id, "کیبورد حذف شد.", "HTML", nil, bot.remove_keyboard())
```

---

## 🧩 مثال کامل

ربات کامل با `/start`، `/ping`، دکمه‌ها و پاسخ به کلیک:

```lucy
from telefog import Telefog
import system

token = "توکن_ربات_شما"
bot = Telefog.new(token)

bot.set_my_commands([
    {command: "start", description: "شروع کار با ربات"},
    {command: "ping", description: "تست اتصال"}
])

print "ربات آماده است..."

offset = 0
while true
    updates = bot.get_updates(offset, 10, 100)
    if updates != nil and updates["ok"] == true
        for update in updates["result"]
            offset = update["update_id"] + 1

            if update["message"] != nil
                msg = update["message"]
                chat_id = msg["chat"]["id"]
                msg_id = msg["message_id"]

                if msg["text"] != nil
                    text = msg["text"]

                    if text == "/start"
                        bot.send_chat_action(chat_id, "typing")
                        b1 = bot.button_cb("🎲 تاس بنداز", "cmd_dice")
                        b2 = bot.button_url("🌐 گیتهاب", "https://github.com")
                        markup = bot.inline_keyboard([[b1], [b2]])
                        bot.send_message(chat_id, "سلام! یکی را انتخاب کن:", "HTML", msg_id, markup)
                    else if text == "/ping"
                        bot.send_message(chat_id, "🏓 <b>پونگ!</b>", "HTML", msg_id)
                    else
                        bot.send_message(chat_id, "پیام شما: " + text, "", msg_id)
                    end
                end

            else if update["callback_query"] != nil
                cb = update["callback_query"]
                cb_id = cb["id"]
                chat_id = cb["message"]["chat"]["id"]

                if cb["data"] == "cmd_dice"
                    bot.answer_callback_query(cb_id, "تاس انداخته شد! 🎲")
                    bot.send_dice(chat_id, "🎲")
                end
            end
        end
    end
end
```

---

## 🌐 پروکسی

`curl` متغیرهای محیطی پروکسی را می‌خواند. قبل از ساخت ربات ست کنید:

```lucy
import system
system.setenv("https_proxy", "socks5h://127.0.0.1:1080")
system.setenv("http_proxy", "socks5h://127.0.0.1:1080")
system.setenv("ALL_PROXY", "socks5h://127.0.0.1:1080")
```

یا در ترمینال:

```bash
export ALL_PROXY="socks5h://127.0.0.1:1080"
lucy bot.lucy
```

---

## 🐞 دیباگ و رفع خطا

با `bot.debug = true` بدنه‌ی هر درخواست چاپ می‌شود. در صورت خطا هم خودکار بدنه‌ی درخواست چاپ می‌شود.

| پیام | معنی و راه‌حل |
|---|---|
| `no response received` | مشکل شبکه یا پروکسی. `curl` و پروکسی را بررسی کنید. |
| `Non-JSON response received` | پاسخ غیر از تلگرام آمده (معمولاً خطای پروکسی). |
| `401 Unauthorized` | توکن اشتباه است. |
| `403 Forbidden: bot was blocked by the user` | کاربر ربات را بلاک کرده است. |
| `400 chat not found` | `chat_id` اشتباه است یا کاربر هنوز به ربات پیام نداده. |
| `400 can't parse entities` | HTML نامعتبر است (تگ نبسته یا `<` و `&` بدون escape). |
| `400 message text is empty` | متن پیام خالی است. |
| `409 Conflict` | همزمان Webhook فعال است یا ربات در جای دیگری هم `get_updates` می‌زند. |
| `429 Too Many Requests` | ارسال بیش از حد سریع؛ بین پیام‌ها `sleep` بگذارید. |

---

## 📝 نکات مهم

- **ایموجی در نام کاربران:** مفسر Lucy ایموجی‌های ارسالی تلگرام (جفت‌های surrogate) را درست decode نمی‌کند. Telefog قبل از parse آن‌ها را به توکن `[[u+1F916]]` تبدیل می‌کند و هنگام ارسال دوباره به ایموجی درست برمی‌گرداند. در ترمینال ممکن است توکن ببینید، اما در تلگرام ایموجی درست نمایش داده می‌شود.
- **HTML در نام کاربر:** اگر نام کاربر را داخل پیام HTML می‌گذارید و نام شامل `<` یا `&` باشد، تلگرام خطا می‌دهد. در این حالت `parse_mode` را `""` بگذارید.
- **نام متغیر `data`:** در برنامه‌ی خود متغیری به نام `data` نسازید، چون با ماژول `data` تداخل می‌کند (مثلاً از `data_val` استفاده کنید).
- **آپلود فایل:** فقط URL و `file_id` پشتیبانی می‌شود؛ ارسال فایل محلی (multipart) در این نسخه نیست.
- **محدودیت تلگرام:** حداکثر طول متن هر پیام ۴۰۹۶ کاراکتر است.

---

<div align="center">

**POWERED-BY DR.F0G3J3 FOR LUCY**

</div>
