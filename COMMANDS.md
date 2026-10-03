# 📖 Справочник команд SCUM-RCON (Command Reference)

Полное руководство по всем административным и кастомным командам, поддерживаемым серверным модом **SCUM-RCON**.

> 💡 **Примечание по синтаксису:**  
> Все команды можно отправлять как с символом `#` (как в игровом чате), так и без него (например, `ListPlayers` и `#ListPlayers` эквивалентны). Регистр букв не имеет значения.

---

## 📑 Содержание
1. [🏗️ Система улучшения баз (Base Upgrade) — NEW](#1-️-система-улучшения-баз-base-upgrade--new)
2. [🚗 Транспорт и логистика (Vehicles)](#2--транспорт-и-логистика-vehicles)
3. [👥 Игроки, статистика и досье](#3--игроки-статистика-и-досье)
4. [💬 Серверный чат и оповещения](#4--серверный-чат-и-оповещения)
5. [📦 Спавн предметов, контейнеров и лута](#5--спавн-предметов-контейнеров-и-лута)
6. [💰 Экономика и очки славы](#6--экономика-и-очки-славы)
7. [🧭 Телепортация и спасение игроков](#7--телепортация-и-спасение-игроков)
8. [🛡️ Модерация и управление игроками](#8-️-модерация-и-управление-игроками)
9. [🤖 Управление роботами (Sentries) и зомби](#9--управление-роботами-sentries-и-зомби)
10. [🛠️ Квесты и устранение софтлоков (Quest Toolkit)](#10-️-квесты-и-устранение-софтлоков-quest-toolkit)
11. [⚙️ Управление сервером и окружением](#11-️-управление-сервером-и-окружением)

---

## 1. 🏗️ Система улучшения баз (Base Upgrade) — NEW

Асинхронный движок пакетного улучшения элементов базы без зависаний сервера. 

> ⚠️ **Важно:**  
> В игре SCUM поддерживается **ТОЛЬКО ПОЛНЫЙ АПГРЕЙД** сразу до максимального уровня (Бетон / Concrete). Поэтапное улучшение (+1 уровень или до промежуточных материалов) движком SCUM не предусмотрено. Все постройки улучшаются сразу в максимальный тир.

---

### `UpgradeBase`
Запускает полный апгрейд построек базы игрока сразу до максимального уровня (бетон).
* **Синтаксис:**
  ```text
  UpgradeBase <PlayerName | SteamID> [DelayMs]
  ```
  * `DelayMs` (по умолчанию: `300`): задержка между обработкой элементов в миллисекундах для исключения фризов сервера.
* **Примеры:**
  ```text
  UpgradeBase 76561198000000000
  UpgradeBase Survivor 250
  UpgradeBase "Ivan Ivanov" 300
  ```
* **Ответ сервера:**
  ```text
  [BaseUpgrade] Started full upgrade of Survivor's base to Concrete (250ms/elem). Use 'UpgradeBaseStatus' to track.
  ```

---

### `UpgradeBaseRadius`
Запускает полный апгрейд всех модульных элементов построек в указанном радиусе (в метрах) вокруг игрока до максимального уровня.
* **Синтаксис:**
  ```text
  UpgradeBaseRadius <RadiusMeters> [PlayerName | SteamID] [DelayMs]
  ```
* **Примеры:**
  ```text
  UpgradeBaseRadius 50 Survivor 200
  UpgradeBaseRadius 30 76561198000000000
  ```

---

### `UpgradeBaseStatus`
Показывает текущее состояние активного процесса апгрейда: сколько элементов улучшено, сколько осталось, процент выполнения и расчетное время.
* **Синтаксис:** `UpgradeBaseStatus`
* **Пример вывода:**
  ```text
  [BaseUpgrade] In Progress: 142/350 elements (40.5%) | Target: Concrete | Delay: 250ms | Elapsed: 35s | Remaining: ~52s
  ```

---

### `UpgradeBaseStop` / `CancelUpgradeBase`
Безопасно прерывает текущий процесс улучшения построек. Уже улучшенные блоки сохраняются.
* **Синтаксис:** `UpgradeBaseStop` или `CancelUpgradeBase`
* **Ответ сервера:** `[BaseUpgrade] Upgrade job cancelled by administrator.`

---

## 2. 🚗 Транспорт и логистика (Vehicles)

### `BringVehicle`
Интеллектуальная доставка любого транспорта прямо к персонажу (автомобиль появляется на расстоянии ~6.5 м прямо перед игроком на уровне земли). Новые координаты сразу фиксируются в `SCUM.db`.
* **Синтаксис:**
  ```text
  BringVehicle <VehicleId> [PlayerName | SteamID]
  ```
* **Двухрежимная механика:**
  * **Автомобиль в памяти (Live):** телепортируется мгновенно прямо к игроку. Персонаж игрока остаётся неподвижным (**0 перемещений**).
  * **Автомобиль в выгруженном секторе (Dormant):** неблокирующий стейт-машин стримит сектор, спавнит авто в памяти, переносит его к игроку и сохраняет координаты.
* **Примеры:**
  ```text
  BringVehicle 20164 Survivor
  BringVehicle 150024 76561198000000000
  ```
* **Ответ:**
  ```text
  BringVehicle: brought BPC_Laika (ID 20164) to Survivor (76561198000000000) at {55756.7, 383588.8, 49196.1} [actor teleported live in world]
  ```

---

### `ListSpawnedVehicles` (алиас: `ListVehicles`)
Выводит полный перечень всей созданной на сервере техники с координатами, типами, пользовательскими именами и владельцами. Данные читаются напрямую из `SCUM.db` в WAL-режиме без нагрузки на FPS сервера.
* **Синтаксис:** `ListSpawnedVehicles`
* **Формат вывода:**
  ```text
  ID <id> | <VehicleClass> | name: <CustomName> | (<X>, <Y>, <Z>) | owner: <OwnerName> (db id <OwnerDbID>)
  ```
* **Пример вывода:**
  ```text
  ID 20164 | BPC_Laika_ES_C | name: Laika | (125400, -34200, 1500) | owner: Survivor (db id 4)
  ID 250005 | BPC_Cruiser_ES_C | name: Cruiser | (130100, -32800, 1450) | owner: Survivor (db id 4)
  ID 300122 | BPC_Wolfswagen_ES_C | name: Wolf | (-45000, 89000, 2100) | owner: unowned (db id 0)
  ```

---

### `SpawnVehicle`
Создает новый экземпляр транспорта по координатам или рядом с игроком.
* **Синтаксис:**
  ```text
  SpawnVehicle <VehicleClass> Location "<X> <Y> <Z>"
  SpawnVehicle <VehicleClass> <SteamID | PlayerName>
  ```
* **Примеры:**
  ```text
  SpawnVehicle BPC_Laika Location "125400 -34200 1500"
  SpawnVehicle BPC_Dirtbike 76561198000000000
  ```

---

### `DestroyAllVehicles`
Удаляет весь транспорт на сервере, не привязанный к базам или замкам.
* **Синтаксис:** `DestroyAllVehicles`

---

## 3. 👥 Игроки, статистика и досье

### `ListPlayers`
Возвращает список всех активных игроков онлайн с пингом и идентификаторами. Ответ отправляется **только в RCON** (не засоряет игровой чат сервера).
* **Синтаксис:** `ListPlayers`
* **Пример вывода:**
  ```text
  76561198000000000 | Survivor | ping: 24ms
  76561198000000001 | Hunter | ping: 55ms
  ```

---

### `Whois` (алиасы: `Dossier`, `PlayerInfo`)
Мгновенное подробное досье любого игрока сервера. Работает как для **онлайн**, так и для **оффлайн** игроков!
* **Синтаксис:**
  ```text
  Whois <SteamID | PlayerName>
  ```
* **Пример:** `Whois Survivor` или `Whois 76561198000000000`
* **Пример вывода:**
  ```text
  [WHOIS] Profile for: Survivor (SteamID: 76561198000000000)
    Status: ONLINE | Location: X=125400.12 Y=-34200.54 Z=1500.00
    Fame: 1,540
    Money: $4,500 (Wallet: $1,500 | Bank: $3,000 | Gold: 12)
    Kills: 8 | Deaths: 2 | K/D: 4.00
    Puppet Kills: 142 | Headshots: 45
    Playtime: 58.4 hrs
    Squad: NightRaiders
    Vehicles Owned (2): #20164: BPC_Laika, #250005: BPC_Cruiser
  ```

---

### `ListSquads`
Выводит список всех зарегистрированных отрядов сервера, их лидеров, славу и список всех участников.
* **Синтаксис:** `ListSquads`
* **Пример вывода:**
  ```text
  Squad [1] "NightRaiders" | Leader: Survivor (76561198000000000) | Members: 3 | Fame: 2100
    - Survivor (Leader)
    - Ghost (Member)
    - Hunter (Member)
  ```

---

### `ListFlags`
Выводит список установленных баз и флагов на сервере с координатами и идентификаторами владельцев.
* **Синтаксис:** `ListFlags`
* **Пример вывода:**
  ```text
  Flag #1 | Squad: NightRaiders | Owner: Survivor (76561198000000000) | Pos: (125000, -34000, 1500)
  ```

---

## 4. 💬 Серверный чат и оповещения

### `SendChat`
Отправляет сообщение в игровой чат от имени системы/сервера в указанный канал.
* **Синтаксис:**
  ```text
  SendChat <ChatType> <Message> [TargetSteamID]
  ```
* **Каналы (ChatType):**
  * `0` — **Global** (Общий глобальный чат)
  * `1` — **Local** (Локальный чат вокруг персонажа)
  * `2` — **Squad** (Чат отряда)
  * `3` — **Admin** (Административный чат)
  * `4` — **Private** (Личное сообщение конкретному игроку по `TargetSteamID`)
* **Примеры:**
  ```text
  SendChat 0 "Внимание: Плановый перезапуск сервера через 15 минут!"
  SendChat 4 "Вам начислен ежедневный бонус: $1,000" 76561198000000000
  ```

---

### `Announce`
Выводит системное оповещение жирным текстом по центру экрана всех игроков.
* **Синтаксис:** `Announce <Message>`
* **Пример:** `Announce Рестарт сервера через 5 минут!`

---

## 5. 📦 Спавн предметов, контейнеров и лута

### `SpawnItem`
Спавнит предмет в мире по координатам или прямо перед указанным игроком.
* **Синтаксис:**
  ```text
  SpawnItem <ItemClass> [Count] [Health] [Ammo] [Location "<X> <Y> <Z>" | TargetSteamID]
  ```
* **Примеры:**
  ```text
  SpawnItem Apple 5 76561198000000000
  SpawnItem Weapon_AK47 1 100 30 Location "125400 -34200 1500"
  SpawnItem Lockpick_Advanced_Item 3 Survivor
  ```

---

### `SpawnInventoryFullOf`
Спавнит контейнер (сундук, рюкзак, шкаф), полностью заполненный указанным предметом.
* **Синтаксис:**
  ```text
  SpawnInventoryFullOf <ContainerClass> <Count> <ItemClass> Location "<X> <Y> <Z>"
  SpawnInventoryFullOf <ContainerClass> <Count> <ItemClass> <SteamID | PlayerName>
  ```
* **Примеры:**
  ```text
  SpawnInventoryFullOf BP_WoodenChest 50 BPC_Ammo_7_62x39mm Location "125400 -34200 1500"
  SpawnInventoryFullOf BP_MetalChest 20 BPC_Weapon_M4A1 76561198000000000
  ```

---

## 6. 💰 Экономика и очки славы

### `SetFamePoints` / `ChangeFamePoints`
* `SetFamePoints <Amount> [SteamID]` — **перезаписывает** количество очков славы абсолютным значением.
* `ChangeFamePoints <+Amount | -Amount> [SteamID]` — **суммирует** или вычитает очки славы от текущего баланса игрока (рекомендуется для наград и голосований!).
* **Примеры:**
  ```text
  ChangeFamePoints +50 76561198000000000
  SetFamePoints 1000 Survivor
  ```

---

### `SetCurrencyBalance` / `ChangeCurrencyBalance`
* **Типы валюты:** `Cash` (наличные/кошелек), `Gold` (золото).
* **Синтаксис:**
  ```text
  SetCurrencyBalance <Cash|Gold> <Amount> [SteamID]
  ChangeCurrencyBalance <Cash|Gold> <+Amount|-Amount> [SteamID]
  ```
* **Примеры:**
  ```text
  ChangeCurrencyBalance Cash +1500 76561198000000000
  ChangeCurrencyBalance Gold +5 Survivor
  SetCurrencyBalance Cash 10000 76561198000000000
  ```

---

## 7. 🧭 Телепортация и спасение игроков

### `Unstuck`
Безопасно перемещает застрявшего персонажа игрока вверх на +1.5–2 метра без риска провалиться под текстуры.
* **Синтаксис:** `Unstuck <PlayerName | SteamID>`
* **Пример:** `Unstuck Survivor`
* **Ответ:** `Unstuck: successfully unstuck Survivor (76561198000000000) from {1250, 450, 100} to {1250, 450, 250}`

---

### `Teleport`
Телепортирует персонажа в указанные 3D координаты.
* **Синтаксис:** `Teleport <X> <Y> <Z> [SteamID]`
* **Пример:** `Teleport 125400 -34200 1500 76561198000000000`

---

### `TeleportTo`
Телепортирует одного игрока к другому игроку.
* **Синтаксис:** `TeleportTo <PlayerName|SteamID> <TargetPlayerName|TargetSteamID>`
* **Пример:** `TeleportTo Hunter Survivor`

---

## 8. 🛡️ Модерация и управление игроками

### `Kick`
Принудительно отключает игрока от сервера.
* **Синтаксис:** `Kick <SteamID | PlayerName> [Reason]`
* **Пример:** `Kick 76561198000000000 "AFK"`

---

### `Ban` / `Unban`
* `Ban <SteamID | PlayerName> [Reason]` — банит игрока на сервере с записью в `BannedUsers.ini`.
* `Unban <SteamID>` — снимает бан с игрока.
* **Примеры:**
  ```text
  Ban 76561198000000001 "Читы / Wallhack"
  Unban 76561198000000001
  ```

---

### `Silence` / `Unsilence`
* `Silence <SteamID> [DurationMinutes]` — блокирует возможность писать в чат.
* `Unsilence <SteamID>` — снимает мут чата.
* **Пример:** `Silence 76561198000000000 60`

---

### `SetGodMode`
Включает или выключает режим бессмертия для персонажа.
* **Синтаксис:** `SetGodMode [SteamID] <True | False>`
* **Пример:** `SetGodMode 76561198000000000 True`

---

## 9. 🤖 Управление роботами (Sentries) и зомби

### `ListSentries`
Выводит список всех активных военных роботов на карте с их координатами и состоянием здоровья.
* **Синтаксис:** `ListSentries`

---

### `DestroySentriesWithinRadius`
Уничтожает всех роботов в заданном радиусе (в см/метрах) от указанной точки.
* **Синтаксис:** `DestroySentriesWithinRadius <Radius> <X> <Y> <Z>`
* **Пример:** `DestroySentriesWithinRadius 5000 150200 -82000 4200`

---

### `SuppressSentryRespawn`
Временно подавляет автоматический респавн роботов.
* **Синтаксис:** `SuppressSentryRespawn <on | off>`

---

### `SetSectorScanEnabled`
Включает или выключает меха роботов по секторам на сервере.
* **Синтаксис:** `SetSectorScanEnabled <True | False>`

---

### `DestroyZombiesWithinRadius`
Уничтожает всех бродячих марионеток (зомби) в заданном радиусе.
* **Синтаксис:** `DestroyZombiesWithinRadius <Radius>`
* **Пример:** `DestroyZombiesWithinRadius 10000`

---

## 10. 🛠️ Квесты и устранение софтлоков (Quest Toolkit)

Набор инструментов для защиты сервера от крашей и «софтлоков» (когда персонаж не может войти на сервер из-за поврежденного или зависшего квеста в `SCUM.db`).

### `FindQuestLockouts`
Сканирует базу данных на наличие персонажей с активными проблемными квестами (список которых задан в `config.ini` секции `[quests] blocked`).
* **Синтаксис:** `FindQuestLockouts`

---

### `DeleteActiveQuestsForUser`
Безопасно удаляет зависшие квесты у конкретного игрока по его SteamID, позволяя ему сразу войти на сервер без сброса персонажа.
* **Синтаксис:** `DeleteActiveQuestsForUser <17-digit SteamID>`
* **Пример:** `DeleteActiveQuestsForUser 76561198000000000`
* **Ответ:** `questdb: deleted 2 active_quest row(s) for SteamID 76561198000000000`

---

### `RunQuestUnstick`
Запускает массовую процедуру авто-очистки зависших квестов для всех обнаруженных проблемных профилей игроков.
* **Синтаксис:** `RunQuestUnstick`

---

## 11. ⚙️ Управление сервером и окружением

### `SetTime`
Устанавливает игровое время суток на сервере.
* **Синтаксис:** `SetTime <Hour> [Minute]`
* **Пример:** `SetTime 12 00` (полдень)

---

### `SetWeather`
Управляет погодными условиями (облачность / осадки) от `0.0` (ясно) до `1.0` (гроза).
* **Синтаксис:** `SetWeather <Value>`
* **Пример:** `SetWeather 0`

---

### `RestartServer` / `ShutdownServer`
Запускает таймер плановой перезагрузки или выключения сервера с уведомлением игроков.
* **Синтаксис:**
  ```text
  RestartServer <Seconds>
  ShutdownServer <Seconds>
  ```
* **Пример:** `RestartServer 60`

---

### `ListCommands`
Выводит список всех доступных команд, зарегистрированных в текущий момент на сервере.
* **Синтаксис:** `ListCommands`
