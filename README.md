# WAKA Lua API Documentation

## 📖 Полная документация Lua API для WAKA CS2

---

## 📋 Содержание

- [Введение](#введение)
- [Быстрый старт](#быстрый-старт)
- [API Reference](#api-reference)
  - [UI (Интерфейс)](#ui-интерфейс)
  - [Client (Клиент)](#client-клиент)
  - [Engine (Движок)](#engine-движок)
  - [Entity (Сущности)](#entity-сущности)
  - [Pawn (Игрок)](#pawn-игрок)
  - [Controller (Контроллер)](#controller-контроллер)
  - [Render (Отрисовка)](#render-отрисовка)
  - [Input (Ввод)](#input-ввод)
  - [Cmd (Команды)](#cmd-команды)
  - [Math (Математика)](#math-математика)
- [Константы](#константы)
- [Callbacks (Коллбэки)](#callbacks-коллбэки)
- [Примеры скриптов](#примеры-скриптов)

---

## Введение

WAKA Lua API предоставляет мощный интерфейс для создания скриптов в CS2. Скрипты позволяют:

- Создавать собственные визуальные элементы (ESP, индикаторы)
- Добавлять пользовательские UI элементы в меню
- Обрабатывать игровые события
- Получать информацию об игроках и мире
- Управлять вводом игрока

### Требования

- Скрипты должны находиться в папке ```C:\waka\lua\```
- Файлы должны иметь расширение ```.lua```
- Используется Lua 5.1

---

## Быстрый старт

### Создание первого скрипта

1. Создайте файл ```C:\waka\lua\my_script.lua```
2. Добавьте следующий код:

```lua
-- Мой первый скрипт WAKA
print("Hello, WAKA!")

function OnPaint()
    render.text(100, 100, "Hello World!", 255, 255, 255, 255, 16, false, false, true)
end
```

3. В меню WAKA откройте вкладку **Lua**
4. Найдите ```my_script``` и нажмите **Load**

---

## API Reference

---

## UI (Интерфейс)

Функции для создания элементов управления в меню.

### ui.checkbox

Создает чекбокс.

```lua
local enabled = ui.checkbox(id, label, default_value)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| id | string | Уникальный идентификатор |
| label | string | Отображаемое название |
| default_value | boolean | Значение по умолчанию |

**Возвращает:** ```boolean``` - текущее состояние

**Пример:**
```lua
local esp_enabled = ui.checkbox("my_esp", "Enable ESP", false)

function OnPaint()
    esp_enabled = ui.get("my_esp")
    if esp_enabled then
        -- ESP код здесь
    end
end
```

---

### ui.slider_int

Создает слайдер для целых чисел.

```lua
local value = ui.slider_int(id, label, default_value, min, max)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| id | string | Уникальный идентификатор |
| label | string | Отображаемое название |
| default_value | integer | Значение по умолчанию |
| min | integer | Минимальное значение |
| max | integer | Максимальное значение |

**Возвращает:** ```integer``` - текущее значение

**Пример:**
```lua
local fov = ui.slider_int("aim_fov", "Aimbot FOV", 90, 1, 180)

function OnCreateMove()
    fov = ui.get("aim_fov")
    -- использовать fov
end
```

---

### ui.slider_float

Создает слайдер для дробных чисел.

```lua
local value = ui.slider_float(id, label, default_value, min, max)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| id | string | Уникальный идентификатор |
| label | string | Отображаемое название |
| default_value | float | Значение по умолчанию |
| min | float | Минимальное значение |
| max | float | Максимальное значение |

**Возвращает:** ```float``` - текущее значение

**Пример:**
```lua
local smooth = ui.slider_float("aim_smooth", "Smoothness", 5.0, 1.0, 20.0)
```

---

### ui.combo

Создает выпадающий список.

```lua
local selected = ui.combo(id, label, default_index, items)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| id | string | Уникальный идентификатор |
| label | string | Отображаемое название |
| default_index | integer | Индекс по умолчанию (с 0) |
| items | table | Массив строк с вариантами |

**Возвращает:** ```integer``` - индекс выбранного элемента

**Пример:**
```lua
local hitbox = ui.combo("aim_hitbox", "Hitbox", 0, {"Head", "Neck", "Chest", "Pelvis"})

function OnCreateMove()
    hitbox = ui.get("aim_hitbox")
    if hitbox == 0 then
        -- целиться в голову
    elseif hitbox == 1 then
        -- целиться в шею
    end
end
```

---

### ui.multi_combo

Создает множественный выбор (флаги).

```lua
local flags = ui.multi_combo(id, label, default_flags, items)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| id | string | Уникальный идентификатор |
| label | string | Отображаемое название |
| default_flags | integer | Битовые флаги по умолчанию |
| items | table | Массив строк с вариантами |

**Возвращает:** ```integer``` - битовые флаги выбранных элементов

**Пример:**
```lua
-- 0 = ничего, 1 = первый, 2 = второй, 3 = оба
local targets = ui.multi_combo("targets", "Targets", 1, {"Enemies", "Teammates"})

function OnPaint()
    targets = ui.get("targets")
    local target_enemies = (targets & 1) ~= 0    -- бит 0
    local target_teammates = (targets & 2) ~= 0  -- бит 1
end
```

---

### ui.color

Создает выбор цвета.

```lua
local r, g, b, a = ui.color(id, label, default_r, default_g, default_b, default_a)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| id | string | Уникальный идентификатор |
| label | string | Отображаемое название |
| default_r | integer | Красный (0-255) |
| default_g | integer | Зеленый (0-255) |
| default_b | integer | Синий (0-255) |
| default_a | integer | Альфа (0-255) |

**Возвращает:** ```r, g, b, a``` - четыре числа (0-255)

**Пример:**
```lua
local r, g, b, a = ui.color("esp_color", "ESP Color", 255, 0, 0, 255)

function OnPaint()
    r, g, b, a = ui.get("esp_color")
    render.rect(100, 100, 200, 200, r, g, b, a)
end
```

---

### ui.button

Создает кнопку.

```lua
local clicked = ui.button(id, label)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| id | string | Уникальный идентификатор |
| label | string | Текст кнопки |

**Возвращает:** ```boolean``` - была ли нажата кнопка

**Пример:**
```lua
local btn = ui.button("reset_btn", "Reset Settings")

function OnPaint()
    if ui.get("reset_btn") then
        ui.set("my_slider", 50)
        print("Settings reset!")
    end
end
```

---

### ui.keybind

Создает привязку клавиши.

```lua
local key = ui.keybind(id, label, default_key)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| id | string | Уникальный идентификатор |
| label | string | Отображаемое название |
| default_key | integer | Код клавиши по умолчанию (0 = нет) |

**Возвращает:** ```integer``` - код виртуальной клавиши

**Пример:**
```lua
local aimkey = ui.keybind("aim_key", "Aim Key", keys.MOUSE4)

function OnCreateMove()
    if ui.is_keybind_active("aim_key") then
        -- aimbot активен
    end
end
```

---

### ui.label

Создает текстовую метку.

```lua
ui.label(id, text)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| id | string | Уникальный идентификатор |
| text | string | Отображаемый текст |

**Пример:**
```lua
ui.label("info", "Script by WAKA")
ui.label("version", "Version 1.0")
```

---

### ui.get

Получает текущее значение UI элемента.

```lua
local value = ui.get(id)
```

**Пример:**
```lua
local enabled = ui.get("my_checkbox")
local slider_val = ui.get("my_slider")
local r, g, b, a = ui.get("my_color")  -- для ColorPicker возвращает 4 значения
```

---

### ui.set

Устанавливает значение UI элемента.

```lua
ui.set(id, value)
ui.set(id, r, g, b, a)  -- для ColorPicker
```

**Пример:**
```lua
ui.set("my_checkbox", true)
ui.set("my_slider", 75)
ui.set("my_color", 255, 128, 0, 255)
```

---

### ui.is_keybind_active

Проверяет, активна ли привязка клавиши.

```lua
local active = ui.is_keybind_active(id)
```

**Возвращает:** ```boolean``` - нажата ли клавиша

**Пример:**
```lua
if ui.is_keybind_active("triggerbot_key") then
    -- triggerbot активен
end
```

---

## Client (Клиент)

Функции для работы с клиентом и системой.

### client.log

Выводит сообщение в консоль скрипта.

```lua
client.log(message)
```

**Пример:**
```lua
client.log("Script initialized!")
client.log("Current FPS: " .. client.get_fps())
```

---

### client.notify

Выводит уведомление.

```lua
client.notify(message)
```

**Пример:**
```lua
client.notify("Target acquired!")
```

---

### client.get_username

Получает имя пользователя Windows.

```lua
local name = client.get_username()
```

**Возвращает:** ```string``` - имя пользователя

**Пример:**
```lua
print("Hello, " .. client.get_username() .. "!")
```

---

### client.get_timestamp

Получает текущее время.

```lua
local time = client.get_timestamp()
```

**Возвращает:** ```string``` - время в формате "HH:MM:SS"

**Пример:**
```lua
print("Current time: " .. client.get_timestamp())
```

---

### client.get_frametime

Получает время последнего кадра.

```lua
local dt = client.get_frametime()
```

**Возвращает:** ```float``` - время в секундах

**Пример:**
```lua
local speed = 100 -- пикселей в секунду
local movement = speed * client.get_frametime()
```

---

### client.get_fps

Получает текущий FPS.

```lua
local fps = client.get_fps()
```

**Возвращает:** ```integer``` - кадров в секунду

---

### client.get_realtime

Получает время с запуска.

```lua
local time = client.get_realtime()
```

**Возвращает:** ```float``` - секунды с запуска

**Пример:**
```lua
-- Мигающий текст
local alpha = math.sin(client.get_realtime() * 5) * 127 + 128
render.text(100, 100, "Blinking!", 255, 255, 255, alpha)
```

---

### client.is_in_game

Проверяет, находится ли игрок в игре.

```lua
local in_game = client.is_in_game()
```

**Возвращает:** ```boolean```

**Пример:**
```lua
function OnPaint()
    if not client.is_in_game() then return end
    -- код ESP
end
```

---

### client.is_connected

Проверяет подключение к серверу.

```lua
local connected = client.is_connected()
```

**Возвращает:** ```boolean```

---

### client.get_map_name

Получает название текущей карты.

```lua
local map = client.get_map_name()
```

**Возвращает:** ```string``` - название карты

**Пример:**
```lua
print("Playing on: " .. client.get_map_name())
```

---

### client.get_screen_size

Получает размер экрана.

```lua
local width, height = client.get_screen_size()
```

**Возвращает:** ```width, height``` - два числа

**Пример:**
```lua
local w, h = client.get_screen_size()
local center_x, center_y = w / 2, h / 2
render.circle(center_x, center_y, 50, 255, 0, 0, 255)
```

---

## Engine (Движок)

Функции для работы с игровым движком.

### engine.get_local_player_index

Получает индекс локального игрока.

```lua
local index = engine.get_local_player_index()
```

**Возвращает:** ```integer``` - индекс игрока

---

### engine.get_max_clients

Получает максимальное количество игроков.

```lua
local max = engine.get_max_clients()
```

**Возвращает:** ```integer``` - максимум игроков

---

### engine.get_view_angles

Получает углы обзора камеры.

```lua
local pitch, yaw, roll = engine.get_view_angles()
```

**Возвращает:** ```pitch, yaw, roll``` - три угла в градусах

**Пример:**
```lua
local pitch, yaw, roll = engine.get_view_angles()
print("Looking at: " .. pitch .. ", " .. yaw)
```

---

### engine.set_view_angles

Устанавливает углы обзора камеры.

```lua
engine.set_view_angles(pitch, yaw, roll)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| pitch | float | Вертикальный угол (-89 до 89) |
| yaw | float | Горизонтальный угол (-180 до 180) |
| roll | float | Наклон (обычно 0) |

**Пример:**
```lua
-- Плавный поворот
local target_yaw = 45
local current_pitch, current_yaw, current_roll = engine.get_view_angles()
local new_yaw = current_yaw + (target_yaw - current_yaw) * 0.1
engine.set_view_angles(current_pitch, new_yaw, 0)
```

---

## Entity (Сущности)

Функции для работы с игровыми сущностями.

### entity.get_local_player

Получает pawn локального игрока.

```lua
local local_pawn = entity.get_local_player()
```

**Возвращает:** ```userdata``` или ```nil```

**Пример:**
```lua
local me = entity.get_local_player()
if me then
    local hp = pawn.get_health(me)
    print("My HP: " .. hp)
end
```

---

### entity.get_local_controller

Получает контроллер локального игрока.

```lua
local local_ctrl = entity.get_local_controller()
```

**Возвращает:** ```userdata``` или ```nil```

---

### entity.get_entity_by_index

Получает сущность по индексу.

```lua
local ent = entity.get_entity_by_index(index)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| index | integer | Индекс сущности (0-65535) |

**Возвращает:** ```userdata``` или ```nil```

---

### entity.get_highest_index

Получает максимальный индекс сущности.

```lua
local max_index = entity.get_highest_index()
```

**Возвращает:** ```integer```

**Пример:**
```lua
-- Итерация по всем сущностям
local max = entity.get_highest_index()
for i = 0, max do
    local ent = entity.get_entity_by_index(i)
    if ent and entity.is_player_controller(ent) then
        -- обработка игрока
    end
end
```

---

### entity.is_player_controller

Проверяет, является ли сущность контроллером игрока.

```lua
local is_ctrl = entity.is_player_controller(ent)
```

**Возвращает:** ```boolean```

---

### entity.is_player_pawn

Проверяет, является ли сущность pawn игрока.

```lua
local is_pawn = entity.is_player_pawn(ent)
```

**Возвращает:** ```boolean```

---

### entity.get_class_name

Получает имя класса сущности.

```lua
local class = entity.get_class_name(ent)
```

**Возвращает:** ```string```

**Пример:**
```lua
local ent = entity.get_entity_by_index(10)
if ent then
    print("Class: " .. entity.get_class_name(ent))
end
```

---

## Pawn (Игрок)

Функции для работы с pawn игрока.

### pawn.get_health

Получает здоровье.

```lua
local hp = pawn.get_health(pawn_ptr)
```

**Возвращает:** ```integer``` (0-100)

---

### pawn.get_max_health

Получает максимальное здоровье.

```lua
local max_hp = pawn.get_max_health(pawn_ptr)
```

**Возвращает:** ```integer```

---

### pawn.get_armor

Получает броню.

```lua
local armor = pawn.get_armor(pawn_ptr)
```

**Возвращает:** ```integer``` (0-100)

---

### pawn.get_team

Получает номер команды.

```lua
local team = pawn.get_team(pawn_ptr)
```

**Возвращает:** ```integer``` (2 = Terrorists, 3 = Counter-Terrorists)

**Пример:**
```lua
local me = entity.get_local_player()
local my_team = pawn.get_team(me)

-- Проверка врага
local enemy_pawn = controller.get_pawn(enemy_ctrl)
if pawn.get_team(enemy_pawn) ~= my_team then
    -- это враг
end
```

---

### pawn.get_origin

Получает позицию игрока.

```lua
local x, y, z = pawn.get_origin(pawn_ptr)
```

**Возвращает:** ```x, y, z``` - три координаты

---

### pawn.get_eye_pos

Получает позицию глаз (откуда стреляет).

```lua
local x, y, z = pawn.get_eye_pos(pawn_ptr)
```

**Возвращает:** ```x, y, z``` - три координаты

---

### pawn.get_velocity

Получает скорость движения.

```lua
local vx, vy, vz = pawn.get_velocity(pawn_ptr)
```

**Возвращает:** ```vx, vy, vz``` - вектор скорости

**Пример:**
```lua
local vx, vy, vz = pawn.get_velocity(me)
local speed = math.sqrt(vx*vx + vy*vy)
print("Speed: " .. math.floor(speed))
```

---

### pawn.get_flags

Получает флаги состояния.

```lua
local player_flags = pawn.get_flags(pawn_ptr)
```

**Возвращает:** ```integer``` - битовые флаги

**Пример:**
```lua
local fl = pawn.get_flags(me)
local on_ground = (fl & flags.FL_ONGROUND) ~= 0
local ducking = (fl & flags.FL_DUCKING) ~= 0
```

---

### pawn.is_alive

Проверяет, жив ли игрок.

```lua
local alive = pawn.is_alive(pawn_ptr)
```

**Возвращает:** ```boolean```

---

### pawn.is_scoped

Проверяет, смотрит ли в прицел.

```lua
local scoped = pawn.is_scoped(pawn_ptr)
```

**Возвращает:** ```boolean```

---

### pawn.is_defusing

Проверяет, разминирует ли бомбу.

```lua
local defusing = pawn.is_defusing(pawn_ptr)
```

**Возвращает:** ```boolean```

---

### pawn.is_on_ground

Проверяет, на земле ли игрок.

```lua
local grounded = pawn.is_on_ground(pawn_ptr)
```

**Возвращает:** ```boolean```

---

### pawn.get_bone_position

Получает позицию кости скелета.

```lua
local x, y, z = pawn.get_bone_position(pawn_ptr, bone_index)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| pawn_ptr | userdata | Указатель на pawn |
| bone_index | integer | Индекс кости (0-27) |

**Индексы костей:**

| Индекс | Кость |
|--------|-------|
| 0 | Pelvis (таз) |
| 4 | Spine (позвоночник) |
| 5 | Neck (шея) |
| 6 | Head (голова) |
| 8 | Left Shoulder |
| 9 | Left Elbow |
| 10 | Left Hand |
| 13 | Right Shoulder |
| 14 | Right Elbow |
| 15 | Right Hand |
| 22 | Left Hip |
| 23 | Left Knee |
| 24 | Left Foot |
| 25 | Right Hip |
| 26 | Right Knee |
| 27 | Right Foot |

**Пример:**
```lua
-- Получить позицию головы
local hx, hy, hz = pawn.get_bone_position(enemy, 6)
local sx, sy, visible = render.world_to_screen(hx, hy, hz)
if visible then
    render.circle(sx, sy, 5, 255, 0, 0, 255)
end
```

---

### pawn.get_shots_fired

Получает количество выстрелов (для контроля отдачи).

```lua
local shots = pawn.get_shots_fired(pawn_ptr)
```

**Возвращает:** ```integer```

---

### pawn.get_active_weapon_name

Получает название активного оружия.

```lua
local weapon = pawn.get_active_weapon_name(pawn_ptr)
```

**Возвращает:** ```string``` - например "AK-47", "AWP", "Knife"

---

### pawn.get_active_weapon_id

Получает ID активного оружия.

```lua
local weapon_id = pawn.get_active_weapon_id(pawn_ptr)
```

**Возвращает:** ```integer``` - ID оружия

---

### pawn.get_active_weapon_clip

Получает патроны в магазине.

```lua
local clip = pawn.get_active_weapon_clip(pawn_ptr)
```

**Возвращает:** ```integer``` - количество патронов

---

## Controller (Контроллер)

Функции для работы с контроллером игрока.

### controller.get_name

Получает имя игрока.

```lua
local name = controller.get_name(ctrl_ptr)
```

**Возвращает:** ```string```

---

### controller.get_pawn

Получает pawn от контроллера.

```lua
local pawn_ptr = controller.get_pawn(ctrl_ptr)
```

**Возвращает:** ```userdata``` или ```nil```

**Пример:**
```lua
local max = entity.get_highest_index()
for i = 0, max do
    local ent = entity.get_entity_by_index(i)
    if ent and entity.is_player_controller(ent) then
        local name = controller.get_name(ent)
        local player_pawn = controller.get_pawn(ent)
        if player_pawn and pawn.is_alive(player_pawn) then
            print(name .. " is alive!")
        end
    end
end
```

---

### controller.get_ping

Получает пинг игрока.

```lua
local ping = controller.get_ping(ctrl_ptr)
```

**Возвращает:** ```integer``` - пинг в мс

---

### controller.get_steam_id

Получает Steam ID игрока.

```lua
local steam_id = controller.get_steam_id(ctrl_ptr)
```

**Возвращает:** ```string``` - Steam ID

---

### controller.is_alive

Проверяет, жив ли игрок.

```lua
local alive = controller.is_alive(ctrl_ptr)
```

**Возвращает:** ```boolean```

---

## Render (Отрисовка)

Функции для рисования на экране. Работают только в ```OnPaint```.

### render.line

Рисует линию.

```lua
render.line(x1, y1, x2, y2, r, g, b, a, thickness)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| x1, y1 | float | Начальная точка |
| x2, y2 | float | Конечная точка |
| r, g, b, a | integer | Цвет (0-255) |
| thickness | float | Толщина линии |

**Пример:**
```lua
render.line(100, 100, 200, 200, 255, 0, 0, 255, 2.0)
```

---

### render.rect

Рисует прямоугольник (контур).

```lua
render.rect(x1, y1, x2, y2, r, g, b, a, rounding, thickness)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| x1, y1 | float | Левый верхний угол |
| x2, y2 | float | Правый нижний угол |
| r, g, b, a | integer | Цвет (0-255) |
| rounding | float | Скругление углов |
| thickness | float | Толщина линии |

**Пример:**
```lua
render.rect(100, 100, 200, 150, 0, 255, 0, 255, 0, 1)
```

---

### render.rect_filled

Рисует залитый прямоугольник.

```lua
render.rect_filled(x1, y1, x2, y2, r, g, b, a, rounding)
```

**Пример:**
```lua
render.rect_filled(100, 100, 200, 150, 255, 0, 0, 128, 5)
```

---

### render.circle

Рисует круг (контур).

```lua
render.circle(x, y, radius, r, g, b, a, segments, thickness)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| x, y | float | Центр круга |
| radius | float | Радиус |
| r, g, b, a | integer | Цвет |
| segments | integer | Количество сегментов (0 = авто) |
| thickness | float | Толщина линии |

**Пример:**
```lua
render.circle(960, 540, 50, 255, 255, 0, 255, 32, 2)
```

---

### render.circle_filled

Рисует залитый круг.

```lua
render.circle_filled(x, y, radius, r, g, b, a, segments)
```

---

### render.text

Рисует текст.

```lua
render.text(x, y, text, r, g, b, a, font_size, center_x, center_y, shadow)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| x, y | float | Позиция текста |
| text | string | Текст |
| r, g, b, a | integer | Цвет |
| font_size | float | Размер шрифта (0 = по умолчанию) |
| center_x | boolean | Центрировать по X |
| center_y | boolean | Центрировать по Y |
| shadow | boolean | Добавить тень |

**Пример:**
```lua
-- Обычный текст
render.text(100, 100, "Hello!", 255, 255, 255, 255, 16, false, false, true)

-- Центрированный текст
local w, h = client.get_screen_size()
render.text(w/2, h/2, "Center", 255, 0, 0, 255, 20, true, true, true)
```

---

### render.text_size

Получает размер текста.

```lua
local width, height = render.text_size(text, font_size)
```

**Возвращает:** ```width, height```

**Пример:**
```lua
local text = "Hello World"
local tw, th = render.text_size(text, 16)
-- Рисуем фон под текст
render.rect_filled(100, 100, 100 + tw + 10, 100 + th + 6, 0, 0, 0, 180)
render.text(105, 103, text, 255, 255, 255, 255, 16, false, false, false)
```

---

### render.triangle

Рисует треугольник (контур).

```lua
render.triangle(x1, y1, x2, y2, x3, y3, r, g, b, a, thickness)
```

---

### render.triangle_filled

Рисует залитый треугольник.

```lua
render.triangle_filled(x1, y1, x2, y2, x3, y3, r, g, b, a)
```

---

### render.world_to_screen

Конвертирует мировые координаты в экранные.

```lua
local screen_x, screen_y, visible = render.world_to_screen(world_x, world_y, world_z)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| world_x, world_y, world_z | float | Мировые координаты |

**Возвращает:** ```screen_x, screen_y, visible```

- ```screen_x, screen_y``` - координаты на экране
- ```visible``` - виден ли объект на экране

**Пример:**
```lua
local wx, wy, wz = pawn.get_origin(enemy)
local sx, sy, vis = render.world_to_screen(wx, wy, wz)
if vis then
    render.text(sx, sy, "Enemy Here", 255, 0, 0, 255, 14, true, false, true)
end
```

---

### render.color

Создает цвет в формате ImU32.

```lua
local color = render.color(r, g, b, a)
```

**Возвращает:** ```integer``` - цвет в формате RGBA

---

## Input (Ввод)

Функции для работы с вводом.

### input.is_key_down

Проверяет, нажата ли клавиша.

```lua
local pressed = input.is_key_down(key_code)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| key_code | integer | Виртуальный код клавиши |

**Возвращает:** ```boolean``` - нажата ли клавиша сейчас

**Пример:**
```lua
if input.is_key_down(keys.SHIFT) then
    print("Shift is held!")
end
```

---

### input.is_key_pressed

Проверяет, была ли клавиша только что нажата.

```lua
local just_pressed = input.is_key_pressed(key_code)
```

**Возвращает:** ```boolean``` - была ли клавиша нажата в этом кадре

**Пример:**
```lua
if input.is_key_pressed(keys.F) then
    print("F was just pressed!")
end
```

---

### input.get_cursor_pos

Получает позицию курсора.

```lua
local x, y = input.get_cursor_pos()
```

**Возвращает:** ```x, y``` - координаты курсора

---

### input.is_menu_open

Проверяет, открыто ли меню WAKA.

```lua
local menu_open = input.is_menu_open()
```

**Возвращает:** ```boolean```

**Пример:**
```lua
function OnPaint()
    if input.is_menu_open() then
        return -- не рисовать ESP когда меню открыто
    end
    -- ESP код
end
```

---

## Cmd (Команды)

Функции для управления вводом игрока. Работают только в ```OnCreateMove```.

### cmd.get_buttons

Получает текущие нажатые кнопки.

```lua
local btns = cmd.get_buttons()
```

**Возвращает:** ```integer``` - битовые флаги кнопок

---

### cmd.set_buttons

Устанавливает кнопки.

```lua
cmd.set_buttons(buttons)
```

---

### cmd.add_button

Добавляет кнопку (нажимает).

```lua
cmd.add_button(button)
```

**Пример:**
```lua
function OnCreateMove()
    local me = entity.get_local_player()
    if not me then return end
    
    -- Автоматический бандихоп
    if input.is_key_down(keys.SPACE) then
        if pawn.is_on_ground(me) then
            cmd.add_button(buttons.IN_JUMP)
        else
            cmd.remove_button(buttons.IN_JUMP)
        end
    end
end
```

---

### cmd.remove_button

Удаляет кнопку (отпускает).

```lua
cmd.remove_button(button)
```

---

## Math (Математика)

Математические функции. Таблица называется ```waka_math```.

### waka_math.vec_length

Вычисляет длину вектора.

```lua
local length = waka_math.vec_length(x, y, z)
```

**Пример:**
```lua
local vx, vy, vz = pawn.get_velocity(me)
local speed = waka_math.vec_length(vx, vy, 0)  -- горизонтальная скорость
```

---

### waka_math.vec_distance

Вычисляет расстояние между точками.

```lua
local dist = waka_math.vec_distance(x1, y1, z1, x2, y2, z2)
```

**Пример:**
```lua
local mx, my, mz = pawn.get_origin(me)
local ex, ey, ez = pawn.get_origin(enemy)
local distance = waka_math.vec_distance(mx, my, mz, ex, ey, ez)
print("Distance to enemy: " .. math.floor(distance))
```

---

### waka_math.normalize_angle

Нормализует угол в диапазон -180 до 180.

```lua
local normalized = waka_math.normalize_angle(angle)
```

**Пример:**
```lua
local yaw = 270
local normalized = waka_math.normalize_angle(yaw)  -- -90
```

---

## Константы

### buttons

Константы кнопок для ```cmd```.

| Константа | Описание |
|-----------|----------|
| ```buttons.IN_ATTACK``` | Основная атака (ЛКМ) |
| ```buttons.IN_ATTACK2``` | Вторичная атака (ПКМ) |
| ```buttons.IN_JUMP``` | Прыжок |
| ```buttons.IN_DUCK``` | Присед |
| ```buttons.IN_FORWARD``` | Вперед |
| ```buttons.IN_BACK``` | Назад |
| ```buttons.IN_MOVELEFT``` | Влево |
| ```buttons.IN_MOVERIGHT``` | Вправо |
| ```buttons.IN_USE``` | Использовать |
| ```buttons.IN_RELOAD``` | Перезарядка |
| ```buttons.IN_SPEED``` | Бег |
| ```buttons.IN_SCOPE``` | Прицел |

---

### flags

Флаги состояния игрока.

| Константа | Описание |
|-----------|----------|
| ```flags.FL_ONGROUND``` | На земле |
| ```flags.FL_DUCKING``` | Присел |
| ```flags.FL_INWATER``` | В воде |

---

### keys

Коды виртуальных клавиш.

| Константа | Клавиша |
|-----------|---------|
| ```keys.LBUTTON``` | Левая кнопка мыши |
| ```keys.RBUTTON``` | Правая кнопка мыши |
| ```keys.MBUTTON``` | Средняя кнопка мыши |
| ```keys.MOUSE4``` | Кнопка мыши 4 |
| ```keys.MOUSE5``` | Кнопка мыши 5 |
| ```keys.SPACE``` | Пробел |
| ```keys.SHIFT``` | Shift |
| ```keys.CTRL``` | Control |
| ```keys.ALT``` | Alt |
| ```keys.TAB``` | Tab |
| ```keys.ESCAPE``` | Escape |
| ```keys.A``` - ```keys.Z``` | Буквы A-Z |
| ```keys.0``` - ```keys.9``` | Цифры 0-9 |
| ```keys.F1``` - ```keys.F12``` | Функциональные клавиши |

---

## Callbacks (Коллбэки)

### OnPaint

Вызывается каждый кадр для отрисовки.

```lua
function OnPaint()
    -- код отрисовки
end
```

**Важно:** Только здесь работают функции ```render.*```

---

### OnCreateMove

Вызывается каждый тик для управления вводом.

```lua
function OnCreateMove()
    -- код управления
end
```

**Важно:** Только здесь работают функции ```cmd.*```

---

### OnPresent

Вызывается каждый кадр (без DrawList).

```lua
function OnPresent()
    -- код обновления
end
```

---

## Примеры скриптов

### Пример 1: Простой ESP

```lua
-- Simple ESP Script
local enabled = ui.checkbox("esp_enabled", "Enable ESP", true)
local box_color_r, box_color_g, box_color_b, box_color_a = ui.color("box_color", "Box Color", 255, 0, 0, 255)

function OnPaint()
    if not ui.get("esp_enabled") then return end
    if not client.is_in_game() then return end
    
    local me = entity.get_local_player()
    if not me then return end
    
    local my_team = pawn.get_team(me)
    local r, g, b, a = ui.get("box_color")
    
    local max = entity.get_highest_index()
    for i = 0, max do
        local ent = entity.get_entity_by_index(i)
        if ent and entity.is_player_controller(ent) then
            local player_pawn = controller.get_pawn(ent)
            if player_pawn and player_pawn ~= me and pawn.is_alive(player_pawn) then
                if pawn.get_team(player_pawn) ~= my_team then
                    local ox, oy, oz = pawn.get_origin(player_pawn)
                    local hx, hy, hz = pawn.get_bone_position(player_pawn, 6)
                    
                    local sx, sy, sv = render.world_to_screen(ox, oy, oz)
                    local hsx, hsy, hsv = render.world_to_screen(hx, hy, hz + 8)
                    
                    if sv and hsv then
                        local height = sy - hsy
                        local width = height * 0.4
                        
                        render.rect(hsx - width/2, hsy, hsx + width/2, sy, r, g, b, a, 0, 1)
                    end
                end
            end
        end
    end
end

print("Simple ESP loaded!")
```

---

### Пример 2: Skeleton ESP

```lua
-- Skeleton ESP Script
local enabled = ui.checkbox("skeleton_enabled", "Enable Skeleton", false)
local enemy_only = ui.checkbox("skeleton_enemy_only", "Enemies Only", true)
local thickness = ui.slider_float("skeleton_thick", "Thickness", 1.5, 0.5, 3.0)
local er, eg, eb, ea = ui.color("skeleton_enemy_color", "Enemy Color", 255, 100, 100, 255)
local tr, tg, tb, ta = ui.color("skeleton_team_color", "Team Color", 100, 100, 255, 255)

local bone_connections = {
    {6, 5}, {5, 4}, {4, 0},           -- Head -> Neck -> Spine -> Pelvis
    {5, 8}, {8, 9}, {9, 10},          -- Left arm
    {5, 13}, {13, 14}, {14, 15},      -- Right arm
    {0, 22}, {22, 23}, {23, 24},      -- Left leg
    {0, 25}, {25, 26}, {26, 27}       -- Right leg
}

local function draw_skeleton(player_pawn, r, g, b, a, thick)
    for _, conn in ipairs(bone_connections) do
        local x1, y1, z1 = pawn.get_bone_position(player_pawn, conn[1])
        local x2, y2, z2 = pawn.get_bone_position(player_pawn, conn[2])
        
        if x1 == 0 and y1 == 0 and z1 == 0 then goto continue end
        if x2 == 0 and y2 == 0 and z2 == 0 then goto continue end
        
        local sx1, sy1, v1 = render.world_to_screen(x1, y1, z1)
        local sx2, sy2, v2 = render.world_to_screen(x2, y2, z2)
        
        if v1 and v2 then
            render.line(sx1, sy1, sx2, sy2, r, g, b, a, thick)
        end
        
        ::continue::
    end
end

function OnPaint()
    if not ui.get("skeleton_enabled") then return end
    if not client.is_in_game() then return end
    
    local me = entity.get_local_player()
    if not me then return end
    
    local my_team = pawn.get_team(me)
    local only_enemies = ui.get("skeleton_enemy_only")
    local thick = ui.get("skeleton_thick")
    local enemy_r, enemy_g, enemy_b, enemy_a = ui.get("skeleton_enemy_color")
    local team_r, team_g, team_b, team_a = ui.get("skeleton_team_color")
    
    local max = entity.get_highest_index()
    for i = 0, max do
        local ent = entity.get_entity_by_index(i)
        if ent and entity.is_player_controller(ent) then
            local player_pawn = controller.get_pawn(ent)
            if player_pawn and player_pawn ~= me and pawn.is_alive(player_pawn) then
                local team = pawn.get_team(player_pawn)
                local is_enemy = team ~= my_team
                
                if only_enemies and not is_enemy then goto next end
                
                local r, g, b, a
                if is_enemy then
                    r, g, b, a = enemy_r, enemy_g, enemy_b, enemy_a
                else
                    r, g, b, a = team_r, team_g, team_b, team_a
                end
                
                draw_skeleton(player_pawn, r, g, b, a, thick)
            end
        end
        ::next::
    end
end

print("Skeleton ESP loaded!")
```

---

### Пример 3: Bunny Hop

```lua
-- Bunny Hop Script
local enabled = ui.checkbox("bhop_enabled", "Enable Bunny Hop", false)
local key = ui.keybind("bhop_key", "Bhop Key", keys.SPACE)

function OnCreateMove()
    if not ui.get("bhop_enabled") then return end
    if not client.is_in_game() then return end
    
    local me = entity.get_local_player()
    if not me then return end
    if not pawn.is_alive(me) then return end
    
    local bhop_key = ui.get("bhop_key")
    if bhop_key == 0 then return end
    
    if input.is_key_down(bhop_key) then
        if pawn.is_on_ground(me) then
            cmd.add_button(buttons.IN_JUMP)
        else
            cmd.remove_button(buttons.IN_JUMP)
        end
    end
end

print("Bunny Hop loaded!")
```

---

### Пример 4: Watermark

```lua
-- Watermark Script
local enabled = ui.checkbox("wm_enabled", "Enable Watermark", true)
local show_fps = ui.checkbox("wm_fps", "Show FPS", true)
local show_time = ui.checkbox("wm_time", "Show Time", true)

function OnPaint()
    if not ui.get("wm_enabled") then return end
    
    local text = "WAKA"
    
    if ui.get("wm_fps") then
        text = text .. " | " .. client.get_fps() .. " fps"
    end
    
    if ui.get("wm_time") then
        text = text .. " | " .. client.get_timestamp()
    end
    
    local tw, th = render.text_size(text, 14)
    local x, y = 10, 10
    
    -- Фон
    render.rect_filled(x - 5, y - 3, x + tw + 5, y + th + 3, 20, 20, 20, 200, 4)
    render.rect(x - 5, y - 3, x + tw + 5, y + th + 3, 100, 100, 255, 255, 4, 1)
    
    -- Текст
    render.text(x, y, text, 255, 255, 255, 255, 14, false, false, false)
end

print("Watermark loaded!")
```

---

### Пример 5: Velocity Indicator

```lua
-- Velocity Indicator Script
local enabled = ui.checkbox("vel_enabled", "Enable Velocity", false)

function OnPaint()
    if not ui.get("vel_enabled") then return end
    if not client.is_in_game() then return end
    
    local me = entity.get_local_player()
    if not me then return end
    if not pawn.is_alive(me) then return end
    
    local vx, vy, vz = pawn.get_velocity(me)
    local speed = math.floor(math.sqrt(vx*vx + vy*vy))
    
    local w, h = client.get_screen_size()
    local text = tostring(speed)
    
    -- Цвет зависит от скорости
    local r, g, b = 255, 255, 255
    if speed > 250 then
        r, g, b = 0, 255, 0
    elseif speed > 200 then
        r, g, b = 255, 255, 0
    end
    
    render.text(w/2, h - 100, text, r, g, b, 255, 32, true, false, true)
end

print("Velocity Indicator loaded!")
```

---

### Пример 6: Spectator List

```lua
-- Spectator List Script
local enabled = ui.checkbox("spec_enabled", "Enable Spectator List", false)

function OnPaint()
    if not ui.get("spec_enabled") then return end
    if not client.is_in_game() then return end
    
    local me = entity.get_local_player()
    if not me then return end
    
    local spectators = {}
    local max = entity.get_highest_index()
    
    for i = 0, max do
        local ent = entity.get_entity_by_index(i)
        if ent and entity.is_player_controller(ent) then
            local player_pawn = controller.get_pawn(ent)
            if player_pawn and not pawn.is_alive(player_pawn) then
                -- Мертвый игрок может быть зрителем
                local name = controller.get_name(ent)
                if name and name ~= "" then
                    table.insert(spectators, name)
                end
            end
        end
    end
    
    if #spectators == 0 then return end
    
    local w, h = client.get_screen_size()
    local x = w - 200
    local y = 100
    
    render.text(x, y, "Spectators:", 255, 255, 255, 255, 16, false, false, true)
    y = y + 20
    
    for _, name in ipairs(spectators) do
        render.text(x, y, name, 255, 200, 200, 255, 14, false, false, true)
        y = y + 16
    end
end

print("Spectator List loaded!")
```

---

## ⚠️ Важные замечания

1. **Безопасность указателей**: Все функции автоматически проверяют валидность указателей. Если pawn/controller невалиден, функции вернут нулевые/пустые значения.

2. **Производительность**: Избегайте тяжелых операций в ```OnPaint``` и ```OnCreateMove```, так как они вызываются каждый кадр/тик.

3. **Ошибки скрипта**: Если скрипт вызывает более 10 ошибок подряд, он автоматически отключается.

4. **SEH Protection**: Все функции защищены от вылетов. Краши в Lua скриптах не приведут к падению игры.

5. **Thread Safety**: UI элементы и состояние скриптов защищены мьютексами.

---

## 📝 Лицензия

Этот API предоставляется "как есть" для использования с WAKA CS2.

---

**WAKA Team © 2026**
