# Справочник команд SCUM-RCON (Command Reference)

Полный список команд, поддерживаемых сервером **SCUM-RCON**, с примерами использования и описанием формата вывода.

---

## 👥 Игроки и статистика

### `ListPlayers`
Выводит список подключенных в данный момент игроков.
* **Синтаксис:** `ListPlayers`
* **Пример вывода:**
  ```text
  76561198156375337 | Domo | ping: 24ms
  76561198000000001 | PlayerTwo | ping: 65ms
  ```

---

### `Whois <SteamID | Name>`
Мгновенное досье игрока по его SteamID64 или точному имени персонажа.
Работает как для **онлайн**, так и для **оффлайн** игроков напрямую из `SCUM.db`.
* **Синтаксис:** `Whois <SteamID | Name>`
* **Пример:** `Whois Domo` или `Whois 76561198156375337`
* **Пример вывода:**
  ```text
  [WHOIS] Profile for: Domo (SteamID: 76561198156375337)
    Fame: 1540
    Money: $4,500 (Wallet: $1,500 | Bank: $3,000 | Gold: 12)
    Kills: 8 | Deaths: 2 | K/D: 4.00
    Puppet Kills: 142 | Headshots: 45
    Playtime: 58.4 hrs
    Squad: NightRaiders
    Vehicles Owned (3): #20164: BPC_Laika, #250005: BPC_Cruiser, #130034: BPC_Dirtbike
  ```

---

### `Unstuck <SteamID | Name>`
Безопасно перемещает застрявшего онлайн-игрока на 2 метра вверх (`Z + 200`).
* **Синтаксис:** `Unstuck <SteamID>`
* **Пример:** `Unstuck 76561198156375337`
* **Ответ:** `[Unstuck] Player Domo teleported 2m up successfully.`

---

## 🚗 Транспорт и база данных

### `ListSpawnedVehicles`
Выводит полный список всего созданного на сервере транспорта с координатами, кастомными именами и владельцем. Формат полностью совместим с SCUM Server Manager (`SSM_RCON`) и кастомными Discord-ботами.
* **Синтаксис:** `ListSpawnedVehicles`
* **Формат строки:**
  ```text
  ID <id> | <VehicleClass> | name: <CustomName> | (<X>, <Y>, <Z>) | owner: <OwnerName> (db id <OwnerDbID>)
  ```
* **Пример вывода:**
  ```text
  ID 20164 | BPC_Laika_ES_C | name: Laika | (125400, -34200, 1500) | owner: Domo (db id 4)
  ID 250005 | BPC_Cruiser_ES_C | name: Cruiser | (130100, -32800, 1450) | owner: Domo (db id 4)
  ID 300122 | BPC_Wolfswagen_ES_C | name: Wolf | (-45000, 89000, 2100) | owner: unowned (db id 0)
  ```

---

### `BringVehicle <VehicleId> [PlayerName | SteamID]`
Доставка любого автомобиля или мотоцикла сервера прямо к игроку (на расстояние ~6.5м на уровне земли) с одновременным сохранением новых координат в `SCUM.db`.
* **Синтаксис:** `BringVehicle <VehicleId> [PlayerName|SteamID]`
* **Поведение:**
  * **Если автомобиль загружен в памяти:** перемещается мгновенно напрямую к игроку. **Игрок остаётся на месте (0 перемещений).**
  * **Если автомобиль выгружен (Dormant):** неблокирующий асинхронный стейт-машин пробуждает сектор автомобиля и после стриминга в память мгновенно переносит его к игроку, возвращая игрока в исходную точку.
* **Пример:** `BringVehicle 20164 Domo`
* **Пример ответа (Live):**
  ```text
  BringVehicle: brought BPC_Laika (ID 20164) to Domo (76561198156375337) at {55756.7, 383588.8, 49196.1} [actor teleported live in world]
  ```
* **Пример ответа (Dormant -> Woken):**
  ```text
  BringVehicle: brought BPC_Laika (ID 20164) to Domo (76561198156375337) at {55756.7, 383588.8, 49196.1} [woken and brought live in 1.1s]
  ```

---

### `ListSquads`
Выводит список всех отрядов сервера, их лидеров и участников.
* **Синтаксис:** `ListSquads`
* **Пример вывода:**
  ```text
  Squad [1] "NightRaiders" | Leader: Domo (76561198156375337) | Members: 3 | Fame: 2100
    - Domo (Leader)
    - Ghost (Member)
    - Hunter (Member)
  ```

---

### `ListFlags`
Выводит список всех установленных баз (флагов) с координатами и владельцами.
* **Синтаксис:** `ListFlags`
* **Пример вывода:**
  ```text
  Flag #1 | Squad: NightRaiders | Owner: Domo (76561198156375337) | Pos: (125000, -34000, 1500)
  ```

---

## 📦 Спавн предметов и контейнеров

### `SpawnInventoryFullOf`
Создает контейнер (ящик, рюкзак, шкаф) и полностью заполняет его указанными предметами.
* **Синтаксис:**
  ```text
  SpawnInventoryFullOf <ContainerClass> <Count> <ItemClass> Location "<X> <Y> <Z>"
  ```
  или возле игрока:
  ```text
  SpawnInventoryFullOf <ContainerClass> <Count> <ItemClass> <SteamID>
  ```
* **Примеры:**
  ```text
  SpawnInventoryFullOf BP_WoodenChest 50 BPC_Weapon_AK47 Location "125400 -34200 1500"
  SpawnInventoryFullOf BP_MetalChest 20 BPC_Ammo_7_62x39mm 76561198156375337
  ```

---

### `SpawnVehicle`
Спавнит транспорт в заданной точке или рядом с игроком.
* **Синтаксис:**
  ```text
  SpawnVehicle <VehicleClass> Location "<X> <Y> <Z>"
  ```
* **Пример:**
  ```text
  SpawnVehicle BPC_Laika Location "125400 -34200 1500"
  ```

---

## 🤖 Роботы / Мехи (Sentries)

### `ListSentries`
Выводит список всех активных военных роботов на карте с их координатами и состоянием.
* **Синтаксис:** `ListSentries`
* **Пример вывода:**
  ```text
  [Sentries] Active: 6
  - Sentry #1 at (150200, -82000, 4200) | Health: 100%
  - Sentry #2 at (151400, -81500, 4200) | Health: 95%
  ```

---

### `DestroySentriesWithinRadius`
Уничтожает всех роботов в указанном радиусе от заданной точки.
* **Синтаксис:** `DestroySentriesWithinRadius <radius> <x> <y> <z>`
* **Пример:**
  ```text
  DestroySentriesWithinRadius 5000 150200 -82000 4200
  ```

---

### `SuppressSentryRespawn`
Временно отключает или включает автоматический респавн мехов на сервере.
* **Синтаксис:** `SuppressSentryRespawn <on | off>`
* **Пример:**
  ```text
  SuppressSentryRespawn on
  ```

---

## 🛠 Квесты и восстановление (Quest Toolkit)

### `FindQuestLockouts`
Сканирует базу `SCUM.db` на наличие игроков, чьи персонажи застряли на проблемных/сломанных квестах (настроенных в `config.ini` в параметре `blocked`).
* **Синтаксис:** `FindQuestLockouts`

---

### `DeleteActiveQuestsForUser <SteamID>`
Безопасно удаляет активные зависшие квесты у указанного игрока в `SCUM.db`.
* **Синтаксис:** `DeleteActiveQuestsForUser <SteamID>`
* **Пример:**
  ```text
  DeleteActiveQuestsForUser 76561198156375337
  ```

---

### `RunQuestUnstick`
Запускает автоматическую процедуру очистки зависших квестов для всех обнаруженных проблемных профилей.
* **Синтаксис:** `RunQuestUnstick`
