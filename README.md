# SCUM-RCON (Dedicated Server RCON Mod)

[![SCUM Version](https://img.shields.io/badge/SCUM-1.3.x%20Compatible-orange.svg)]()
[![Platform](https://img.shields.io/badge/Platform-Windows%20Win64-blue.svg)]()
[![Protocol](https://img.shields.io/badge/Protocol-Valve%20Source%20RCON-green.svg)]()

**SCUM-RCON** — серверный мод на базе UE4SS для выделенных серверов **SCUM**, реализующий полноценный **Source RCON** сервер.

Позволяет удалённо выполнять любые административные и кастомные команды с любого стандартного RCON-клиента, консоли или стороннего бота без необходимости нахождения администратора в игре.

---

## 🌟 Ключевые возможности (Features)

* **Удалённое администрирование через Source RCON**:
  * Поддержка любых стандартных RCON-клиентов (`mcrcon`, `rcon-cli`, BattleMetrics, Web-панелей, Discord/Telegram-ботов).
  * Выполнение команд без присутствия администратора онлайн на сервере.
* **Нативная интеграция с базой данных `SCUM.db`**:
  * `ListSpawnedVehicles` — выводит полный список всего созданного на сервере транспорта с координатами, кастомными именами и базой ID владельцев (`_owningUserProfileId`).
  * `Whois <SteamID | Name>` — мгновенное подробное досье игрока (работает как для онлайн, так и для оффлайн игроков): ник, SteamID, Fame, кошелёк/банк/золото, PvP K/D, убийства зомби/хедшоты, время в игре, отряд и привязанный транспорт.
  * `ListSquads` — единый быстрый запрос списка всех отрядов и их участников.
  * `ListPlayers` — нативное перечисление онлайн игроков без задержек тика.
  * `ListFlags` — список установленных флагов баз с координатами и владельцами.
* **Безопасные спавны с координатами**:
  * Свободное размещение спавнов (`SpawnInventoryFullOf`, `SpawnVehicle`, `SpawnItem`, `SpawnBrenner`, `SpawnRazor` и др.) по заданным координатам `{X=... Y=... Z=...}` или около указанного онлайн-игрока.
  * `SpawnInventoryFullOf <Container> <Count> <Item>` — корректно заполняет контейнеры предметами.
* **Управление военной техникой (Mechs / Sentries)**:
  * `ListSentries` — вывод всех живых роботов-мехов с их координатами.
  * `DestroySentriesWithinRadius <radius> <x> <y> <z>` — очистка мехов в указанном радиусе.
  * `SuppressSentryRespawn <on|off>` — глобальное управление спавном мехов.
* **Устранение софтлоков квестов (Quest Recovery Toolkit)**:
  * `FindQuestLockouts` — поиск игроков, заблокированных проблемными квестами.
  * `DeleteActiveQuestsForUser <SteamID>` / `RunQuestUnstick` — безопасная очистка проблемных квестов на уровне WAL SQLite без остановки сервера.
* **Полная портативность**:
  * Мод не содержит жестко зашитых путей. `SCUM.db` и `config.ini` обнаруживаются динамически.

---

## 🚀 Быстрая установка (Installation)

1. Скачайте архив **`SCUM-RCON-Win64.zip`** из раздела [Releases](https://github.com/AMURKA-PVE-SCUM/scum-rcon/releases) или возьмите файлы из папки `Win64/`.
2. Распакуйте содержимое в каталог `Binaries\Win64` вашего сервера:
   ```text
   <Папка_Сервера>\SCUM\Binaries\Win64\
   ```
   Структура файлов должна выглядеть следующим образом:
   ```text
   SCUM\Binaries\Win64\
   ├── dwmapi.dll                  (загрузчик UE4SS)
   ├── SCUMServer.exe
   └── ue4ss\
       ├── UE4SS.dll
       ├── UE4SS-settings.ini
       └── Mods\
           ├── mods.txt
           └── scum_rcon\
               ├── dlls\
               │   └── main.dll    (мод SCUM-RCON)
               ├── config.ini      (ФАЙЛ НАСТРОЙКИ)
               └── enabled.txt
   ```
3. Откройте `ue4ss\Mods\scum_rcon\config.ini` в текстовом редакторе и задайте надёжный пароль:
   ```ini
   [rcon]
   bind_address = 0.0.0.0
   port         = 28015
   password     = ВашНадежныйПароль
   ```
   > ⚠️ **Важно:** Сервер RCON **не запустится**, пока в параметре `password` остаётся значение по умолчанию `CHANGE_ME_BEFORE_USE`.
4. Запустите сервер `SCUMServer.exe`.
5. Проверьте лог `ue4ss\UE4SS.log`. В нём должна появиться строка:
   ```text
   [SCUM-RCON] SCUM-RCON ready - listening on 0.0.0.0:28015
   ```

---

## ⚙️ Конфигурация (`config.ini`)

```ini
[rcon]
; IP-адрес для прослушивания (0.0.0.0 — все интерфейсы)
bind_address    = 0.0.0.0

; Порт RCON сервера (по умолчанию: 28015)
port            = 28015

; Пароль для подключения (ОБЯЗАТЕЛЬНО СМЕНИТЬ)
password        = YourStrongPasswordHere

; Логирование попыток подключения
auth_log        = true

[quests]
; Список проблемных квестов через запятую
blocked         = 

; Автоматическая очистка заблокированных квестов при старте сервера
auto_unstick             = false
auto_unstick_dry_run     = false
auto_unstick_max_delete  = 100

[logging]
verbose = false
```

---

## 💻 Примеры подключения

### 1. Консольный клиент `mcrcon`
```bash
mcrcon -H 127.0.0.1 -P 28015 -p YourStrongPasswordHere -t
```
После подключения можно вводить команды:
```text
> ListPlayers
> ListSpawnedVehicles
> Whois Domo
```

### 2. Node.js (для ботов Discord / Telegram / Веб-панелей)
```javascript
const { Rcon } = require('rcon-client'); // npm install rcon-client

async function main() {
  const rcon = await Rcon.connect({
    host: '127.0.0.1',
    port: 28015,
    password: 'YourStrongPasswordHere'
  });

  // Получить досье игрока
  const dossier = await rcon.send('Whois 76561198156375337');
  console.log(dossier);

  // Список техники с владельцами
  const vehicles = await rcon.send('ListSpawnedVehicles');
  console.log(vehicles);

  await rcon.end();
}

main().catch(console.error);
```

### 3. Python
```python
import socket
import struct

# Стандартный клиент Valve Source RCON протокола
# Отправляет команды и принимает составные ответы (multi-packet)
```

---

## 📋 Основные команды (Command Reference)

| Команда | Описание |
|---|---|
| `ListPlayers` | Список активных игроков онлайн (SteamID, имена, пинг). |
| `ListSpawnedVehicles` | Список всех машин на карте: ID, тип, кастомное имя, координаты, владелец `Name (db id X)`. |
| `Whois <SteamID \| Name>` | Полное досье игрока (Fame, деньги, банк, золото, K/D, убийства зомби, время, отряд, транспорт). |
| `ListSquads` | Список всех зарегистрированных отрядов с их очками и участниками. |
| `ListFlags` | Список установленных баз и флагов с координатами и ID владельцев. |
| `SpawnInventoryFullOf <Box> <Count> <Item> Location "<Coords>"` | Спавн ящика/контейнера, заполненного указанными предметами. |
| `Unstuck <SteamID>` | Безопасный вертикальный подъём застрявшего игрока на +2 метра. |
| `ListSentries` | Список всех активных роботов-мехов с координатами. |
| `DestroySentriesWithinRadius <radius> <x> <y> <z>` | Уничтожение мехов в радиусе с авто-подавлением респавна. |
| `SuppressSentryRespawn <on\|off>` | Включение/выключение спавна мехов на сервере. |
| `FindQuestLockouts` | Поиск игроков с зависшими квестами из списка `blocked`. |
| `RunQuestUnstick` | Безопасная очистка зависших квестов в базе данных. |

Подробное руководство по всем командам доступно в файле [COMMANDS.md](COMMANDS.md).

---

## 🔒 Безопасность

* Никогда не используйте пароль по умолчанию.
* Если RCON порт открыт наружу, защитите его с помощью брандмауэра (Firewall whitelist) или используйте VPN/SSH-туннель.
* Мод оптимизирован: тяжелые запросы (Whois, списки техники, отряды) читаются напрямую из `SCUM.db` в безопасном WAL-режиме, не блокируя игровой поток (Game Thread Tick).

---

## 📄 Лицензия

* Фреймворк UE4SS распространяется под лицензией MIT.
* SCUM является зарегистрированной торговой маркой Gamepires.
