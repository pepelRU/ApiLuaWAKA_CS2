# WAKA Lua API Documentation

## 📖 Полная документация Lua API для WAKA CS2

---

## 📋 Содержание

- [Введение](#введение)
- [Быстрый старт](#быстрый-старт)
- [API Reference](#api-reference)
  - [UI (Интерфейс)](#ui-интерфейс)
  - [Window (Окна)](#window-окна)
  - [Client (Клиент)](#client-клиент)
  - [Engine (Движок)](#engine-движок)
  - [Entity (Сущности)](#entity-сущности)
  - [Pawn (Игрок)](#pawn-игрок)
  - [Controller (Контроллер)](#controller-контроллер)
  - [Render (Отрисовка)](#render-отрисовка)
  - [Input (Ввод)](#input-ввод)
  - [Cmd (Команды)](#cmd-команды)
  - [Vars (Переменные)](#vars-переменные)
  - [Math (Математика)](#math-математика)
- [Константы](#константы)
- [Callbacks (Коллбэки)](#callbacks-коллбэки)
- [Примеры скриптов](#примеры-скриптов)

---

## Введение

WAKA Lua API предоставляет мощный интерфейс для создания скриптов в CS2. Скрипты позволяют:

- Создавать собственные визуальные элементы (ESP, индикаторы)
- Добавлять пользовательские UI элементы в меню
- Создавать перетаскиваемые окна с элементами управления
- Обрабатывать игровые события
- Получать информацию об игроках и мире
- Управлять вводом игрока

### Требования

- Скрипты должны находиться в папке `C:\waka\lua\`
- Файлы должны иметь расширение `.lua`
- Используется Lua 5.1

---

## Быстрый старт

### Создание первого скрипта

1. Создайте файл `C:\waka\lua\my_script.lua`
2. Добавьте следующий код:

```lua
-- Мой первый скрипт WAKA
print("Hello, WAKA!")

function OnPaint()
    render.text(100, 100, "Hello World!", 255, 255, 255, 255, 16, false, false, true)
end
```

3. В меню WAKA откройте вкладку **Lua**
4. Найдите `my_script` и нажмите **Load**

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

**Возвращает:** `boolean` - текущее состояние

**Пример:**
```lua
local esp_enabled = ui.checkbox("my_esp", "Enable ESP", false)

function OnPaint()
    if ui.get("my_esp") then
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

**Возвращает:** `integer` - текущее значение

**Пример:**
```lua
local fov = ui.slider_int("aim_fov", "Aimbot FOV", 90, 1, 180)
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

**Возвращает:** `float` - текущее значение

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

**Возвращает:** `integer` - индекс выбранного элемента

**Пример:**
```lua
local hitbox = ui.combo("aim_hitbox", "Hitbox", 0, {"Head", "Neck", "Chest", "Pelvis"})
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

**Возвращает:** `integer` - битовые флаги выбранных элементов

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

**Возвращает:** `r, g, b, a` - четыре числа (0-255)

**Пример:**
```lua
local r, g, b, a = ui.color("esp_color", "ESP Color", 255, 0, 0, 255)
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

**Возвращает:** `boolean` - была ли нажата кнопка (сбрасывается после чтения)

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

**Возвращает:** `integer` - код виртуальной клавиши

**Пример:**
```lua
local aimkey = ui.keybind("aim_key", "Aim Key", keys.MOUSE4)
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

Для ColorPicker возвращает 4 значения:

```lua
local r, g, b, a = ui.get("my_color")
```

**Пример:**
```lua
local enabled = ui.get("my_checkbox")
local slider_val = ui.get("my_slider")
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

Проверяет, активна ли привязка клавиши (нажата ли клавиша).

```lua
local active = ui.is_keybind_active(id)
```

**Возвращает:** `boolean` - нажата ли клавиша

**Пример:**
```lua
if ui.is_keybind_active("triggerbot_key") then
    -- triggerbot активен
end
```

---

## Window (Окна)

Функции для создания и управления перетаскиваемыми окнами.

### window.create

Создает перетаскиваемое окно.

```lua
local success = window.create(id, title, x, y, width, height)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| id | string | Уникальный идентификатор окна |
| title | string | Заголовок окна |
| x | float | Начальная позиция X |
| y | float | Начальная позиция Y |
| width | float | Ширина окна |
| height | float | Высота окна |

**Возвращает:** `boolean` - успех создания

**Пример:**
```lua
window.create("main", "My Window", 100, 100, 300, 200)
```

---

### window.set_visible

Устанавливает видимость окна.

```lua
window.set_visible(id, visible)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| id | string | ID окна |
| visible | boolean | Видимо/скрыто |

**Пример:**
```lua
window.set_visible("main", true)
```

---

### window.is_visible

Проверяет видимость окна.

```lua
local visible = window.is_visible(id)
```

**Возвращает:** `boolean`

---

### window.get_pos

Получает позицию окна.

```lua
local x, y = window.get_pos(id)
```

**Возвращает:** `x, y` - координаты окна

---

### window.set_pos

Устанавливает позицию окна.

```lua
window.set_pos(id, x, y)
```

---

### window.get_size

Получает размер окна.

```lua
local width, height = window.get_size(id)
```

---

### window.set_size

Устанавливает размер окна.

```lua
window.set_size(id, width, height)
```

---

### window.set_bg_color

Устанавливает цвет фона окна.

```lua
window.set_bg_color(id, r, g, b, a)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| id | string | ID окна |
| r, g, b, a | integer | Цвет (0-255) |

---

### window.set_border_color

Устанавливает цвет границы окна.

```lua
window.set_border_color(id, r, g, b, a)
```

---

### window.get_content_pos

Получает позицию области содержимого окна (внутренняя область).

```lua
local x, y = window.get_content_pos(id)
```

**Возвращает:** координаты для размещения контента внутри окна

---

### window.get_content_size

Получает размер области содержимого окна.

```lua
local width, height = window.get_content_size(id)
```

**Возвращает:** размеры для размещения контента

**Пример использования окна:**
```lua
-- Создаем окно
window.create("esp_window", "ESP Settings", 100, 100, 300, 200)
window.set_visible("esp_window", true)

function OnPaint()
    if window.is_visible("esp_window") then
        local x, y = window.get_content_pos("esp_window")
        local w, h = window.get_content_size("esp_window")
        
        -- Рисуем контент внутри окна
        render.text(x, y, "ESP Settings", 255, 255, 255, 255)
        render.text(x, y + 30, "Enabled: " .. tostring(ui.get("esp_enabled")), 200, 200, 200, 255)
    end
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

**Возвращает:** `string` - имя пользователя

---

### client.get_timestamp

Получает текущее время.

```lua
local time = client.get_timestamp()
```

**Возвращает:** `string` - время в формате "HH:MM:SS"

---

### client.get_frametime

Получает время последнего кадра.

```lua
local dt = client.get_frametime()
```

**Возвращает:** `float` - время в секундах

---

### client.get_fps

Получает текущий FPS.

```lua
local fps = client.get_fps()
```

**Возвращает:** `integer` - кадров в секунду

---

### client.get_realtime

Получает время с запуска.

```lua
local time = client.get_realtime()
```

**Возвращает:** `float` - секунды с запуска

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

**Возвращает:** `boolean`

---

### client.is_connected

Проверяет подключение к серверу.

```lua
local connected = client.is_connected()
```

**Возвращает:** `boolean`

---

### client.get_map_name

Получает название текущей карты.

```lua
local map = client.get_map_name()
```

**Возвращает:** `string` - название карты

---

### client.get_screen_size

Получает размер экрана.

```lua
local width, height = client.get_screen_size()
```

**Возвращает:** `width, height` - два числа

---

## Engine (Движок)

Функции для работы с игровым движком.

### engine.get_local_player_index

Получает индекс локального игрока.

```lua
local index = engine.get_local_player_index()
```

**Возвращает:** `integer` - индекс игрока

---

### engine.get_max_clients

Получает максимальное количество игроков.

```lua
local max = engine.get_max_clients()
```

**Возвращает:** `integer` - максимум игроков

---

### engine.get_view_angles

Получает углы обзора камеры.

```lua
local pitch, yaw, roll = engine.get_view_angles()
```

**Возвращает:** `pitch, yaw, roll` - три угла в градусах

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

---

## Entity (Сущности)

Функции для работы с игровыми сущностями.

### entity.get_local_player

Получает pawn локального игрока.

```lua
local local_pawn = entity.get_local_player()
```

**Возвращает:** `userdata` или `nil`

---

### entity.get_local_controller

Получает контроллер локального игрока.

```lua
local local_ctrl = entity.get_local_controller()
```

**Возвращает:** `userdata` или `nil`

---

### entity.get_entity_by_index

Получает сущность по индексу.

```lua
local ent = entity.get_entity_by_index(index)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| index | integer | Индекс сущности (0-65535) |

**Возвращает:** `userdata` или `nil`

---

### entity.get_highest_index

Получает максимальный индекс сущности.

```lua
local max_index = entity.get_highest_index()
```

**Возвращает:** `integer`

---

### entity.is_player_controller

Проверяет, является ли сущность контроллером игрока.

```lua
local is_ctrl = entity.is_player_controller(ent)
```

**Возвращает:** `boolean`

---

### entity.is_player_pawn

Проверяет, является ли сущность pawn игрока.

```lua
local is_pawn = entity.is_player_pawn(ent)
```

**Возвращает:** `boolean`

---

### entity.get_class_name

Получает имя класса сущности.

```lua
local class = entity.get_class_name(ent)
```

**Возвращает:** `string`

---

## Pawn (Игрок)

Функции для работы с pawn игрока. Все функции принимают указатель на pawn, полученный из `entity.get_local_player()` или `controller.get_pawn()`.

### pawn.get_health

Получает здоровье.

```lua
local hp = pawn.get_health(pawn_ptr)
```

**Возвращает:** `integer` (0-100)

---

### pawn.get_max_health

Получает максимальное здоровье.

```lua
local max_hp = pawn.get_max_health(pawn_ptr)
```

**Возвращает:** `integer`

---

### pawn.get_armor

Получает броню.

```lua
local armor = pawn.get_armor(pawn_ptr)
```

**Возвращает:** `integer` (0-100)

---

### pawn.get_team

Получает номер команды.

```lua
local team = pawn.get_team(pawn_ptr)
```

**Возвращает:** `integer` (2 = Terrorists, 3 = Counter-Terrorists)

---

### pawn.get_origin

Получает позицию игрока.

```lua
local x, y, z = pawn.get_origin(pawn_ptr)
```

**Возвращает:** `x, y, z` - три координаты

---

### pawn.get_eye_pos

Получает позицию глаз (откуда стреляет).

```lua
local x, y, z = pawn.get_eye_pos(pawn_ptr)
```

**Возвращает:** `x, y, z` - три координаты

---

### pawn.get_velocity

Получает скорость движения.

```lua
local vx, vy, vz = pawn.get_velocity(pawn_ptr)
```

**Возвращает:** `vx, vy, vz` - вектор скорости

---

### pawn.get_flags

Получает флаги состояния.

```lua
local player_flags = pawn.get_flags(pawn_ptr)
```

**Возвращает:** `integer` - битовые флаги

---

### pawn.is_alive

Проверяет, жив ли игрок.

```lua
local alive = pawn.is_alive(pawn_ptr)
```

**Возвращает:** `boolean`

---

### pawn.is_scoped

Проверяет, смотрит ли в прицел.

```lua
local scoped = pawn.is_scoped(pawn_ptr)
```

**Возвращает:** `boolean`

---

### pawn.is_defusing

Проверяет, разминирует ли бомбу.

```lua
local defusing = pawn.is_defusing(pawn_ptr)
```

**Возвращает:** `boolean`

---

### pawn.is_on_ground

Проверяет, на земле ли игрок.

```lua
local grounded = pawn.is_on_ground(pawn_ptr)
```

**Возвращает:** `boolean`

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

---

### pawn.get_shots_fired

Получает количество выстрелов (для контроля отдачи).

```lua
local shots = pawn.get_shots_fired(pawn_ptr)
```

**Возвращает:** `integer`

---

### pawn.get_active_weapon_name

Получает название активного оружия.

```lua
local weapon = pawn.get_active_weapon_name(pawn_ptr)
```

**Возвращает:** `string` - например "AK-47", "AWP", "Knife"

---

### pawn.get_active_weapon_id

Получает ID активного оружия.

```lua
local weapon_id = pawn.get_active_weapon_id(pawn_ptr)
```

**Возвращает:** `integer` - ID оружия

---

### pawn.get_active_weapon_clip

Получает патроны в магазине.

```lua
local clip = pawn.get_active_weapon_clip(pawn_ptr)
```

**Возвращает:** `integer` - количество патронов

---

## Controller (Контроллер)

Функции для работы с контроллером игрока.

### controller.get_name

Получает имя игрока.

```lua
local name = controller.get_name(ctrl_ptr)
```

**Возвращает:** `string`

---

### controller.get_pawn

Получает pawn от контроллера.

```lua
local pawn_ptr = controller.get_pawn(ctrl_ptr)
```

**Возвращает:** `userdata` или `nil`

---

### controller.get_ping

Получает пинг игрока.

```lua
local ping = controller.get_ping(ctrl_ptr)
```

**Возвращает:** `integer` - пинг в мс

---

### controller.get_steam_id

Получает Steam ID игрока.

```lua
local steam_id = controller.get_steam_id(ctrl_ptr)
```

**Возвращает:** `string` - Steam ID

---

### controller.is_alive

Проверяет, жив ли игрок.

```lua
local alive = controller.is_alive(ctrl_ptr)
```

**Возвращает:** `boolean`

---

## Render (Отрисовка)

Функции для рисования на экране. Работают только в `OnPaint`.

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

---

### render.rect_filled

Рисует залитый прямоугольник.

```lua
render.rect_filled(x1, y1, x2, y2, r, g, b, a, rounding)
```

---

### render.gradient_rect_h

Рисует горизонтальный градиент.

```lua
render.gradient_rect_h(x1, y1, x2, y2, r1, g1, b1, a1, r2, g2, b2, a2)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| x1, y1 | float | Левый верхний угол |
| x2, y2 | float | Правый нижний угол |
| r1, g1, b1, a1 | integer | Цвет слева |
| r2, g2, b2, a2 | integer | Цвет справа |

---

### render.gradient_rect_v

Рисует вертикальный градиент.

```lua
render.gradient_rect_v(x1, y1, x2, y2, r1, g1, b1, a1, r2, g2, b2, a2)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| x1, y1 | float | Левый верхний угол |
| x2, y2 | float | Правый нижний угол |
| r1, g1, b1, a1 | integer | Цвет сверху |
| r2, g2, b2, a2 | integer | Цвет снизу |

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

---

### render.circle_filled

Рисует залитый круг.

```lua
render.circle_filled(x, y, radius, r, g, b, a, segments)
```

---

### render.arc

Рисует дугу.

```lua
render.arc(cx, cy, radius, a_min, a_max, r, g, b, a, thickness, segments)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| cx, cy | float | Центр дуги |
| radius | float | Радиус |
| a_min | float | Начальный угол (радианы) |
| a_max | float | Конечный угол (радианы) |
| r, g, b, a | integer | Цвет |
| thickness | float | Толщина |
| segments | integer | Количество сегментов |

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

---

### render.text_size

Получает размер текста.

```lua
local width, height = render.text_size(text, font_size)
```

**Возвращает:** `width, height`

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

### render.polyline

Рисует ломаную линию.

```lua
render.polyline(points, r, g, b, a, thickness, closed)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| points | table | Массив точек {{x1,y1}, {x2,y2}, ...} |
| r, g, b, a | integer | Цвет |
| thickness | float | Толщина |
| closed | boolean | Замкнуть линию |

---

### render.convex_poly_filled

Рисует залитый выпуклый многоугольник.

```lua
render.convex_poly_filled(points, r, g, b, a)
```

---

### render.bezier_cubic

Рисует кубическую кривую Безье.

```lua
render.bezier_cubic(x1, y1, x2, y2, x3, y3, x4, y4, r, g, b, a, thickness, segments)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| x1, y1 | float | Начальная точка |
| x2, y2 | float | Первая контрольная точка |
| x3, y3 | float | Вторая контрольная точка |
| x4, y4 | float | Конечная точка |
| r, g, b, a | integer | Цвет |
| thickness | float | Толщина |
| segments | integer | Количество сегментов |

---

### render.progress_bar

Рисует прогресс-бар.

```lua
render.progress_bar(x, y, width, height, progress, bg_r, bg_g, bg_b, bg_a, fg_r, fg_g, fg_b, fg_a, rounding)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| x, y | float | Позиция |
| width, height | float | Размеры |
| progress | float | Прогресс (0.0 - 1.0) |
| bg_r, bg_g, bg_b, bg_a | integer | Цвет фона |
| fg_r, fg_g, fg_b, fg_a | integer | Цвет заполнения |
| rounding | float | Скругление |

---

### render.shadow

Рисует тень вокруг прямоугольника.

```lua
render.shadow(x1, y1, x2, y2, r, g, b, a, size, rounding)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| x1, y1 | float | Левый верхний угол |
| x2, y2 | float | Правый нижний угол |
| r, g, b, a | integer | Цвет тени |
| size | float | Размер тени |
| rounding | float | Скругление углов |

---

### render.world_to_screen

Конвертирует мировые координаты в экранные.

```lua
local screen_x, screen_y, visible = render.world_to_screen(world_x, world_y, world_z)
```

| Параметр | Тип | Описание |
|----------|-----|----------|
| world_x, world_y, world_z | float | Мировые координаты |

**Возвращает:** `screen_x, screen_y, visible`

- `screen_x, screen_y` - координаты на экране
- `visible` - виден ли объект на экране

---

### render.color

Создает цвет в формате ImU32 (для совместимости).

```lua
local color = render.color(r, g, b, a)
```

**Возвращает:** `integer` - цвет в формате RGBA

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

**Возвращает:** `boolean` - нажата ли клавиша сейчас

---

### input.is_key_pressed

Проверяет, была ли клавиша только что нажата.

```lua
local just_pressed = input.is_key_pressed(key_code)
```

**Возвращает:** `boolean` - была ли клавиша нажата в этом кадре

---

### input.get_cursor_pos

Получает позицию курсора.

```lua
local x, y = input.get_cursor_pos()
```

**Возвращает:** `x, y` - координаты курсора

---

### input.is_menu_open

Проверяет, открыто ли меню WAKA.

```lua
local menu_open = input.is_menu_open()
```

**Возвращает:** `boolean`

---

## Cmd (Команды)

Функции для управления вводом игрока. Работают только в `OnCreateMove`.

### cmd.get_buttons

Получает текущие нажатые кнопки.

```lua
local btns = cmd.get_buttons()
```

**Возвращает:** `integer` - битовые флаги кнопок

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
    if input.is_key_down(keys.SPACE) then
        cmd.add_button(buttons.IN_JUMP)
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

## Vars (Переменные)

Доступ к глобальным переменным WAKA.

### vars.get_esp_enabled / vars.set_esp_enabled

Включение/отключение ESP.

```lua
local enabled = vars.get_esp_enabled()
vars.set_esp_enabled(true)
```

---

### vars.get_esp_box / vars.set_esp_box

Включение/отключение ESP боксов.

```lua
local box = vars.get_esp_box()
vars.set_esp_box(true)
```

---

### vars.get_esp_box_color / vars.set_esp_box_color

Цвет ESP боксов.

```lua
local r, g, b, a = vars.get_esp_box_color()
vars.set_esp_box_color(255, 0, 0, 255)
```

---

### vars.get_esp_name / vars.set_esp_name

Включение/отключение отображения имен.

---

### vars.get_esp_name_color / vars.set_esp_name_color

Цвет имен.

---

### vars.get_esp_hp_bar / vars.set_esp_hp_bar

Включение/отключение полоски здоровья.

---

### vars.get_esp_armor_bar / vars.set_esp_armor_bar

Включение/отключение полоски брони.

---

### vars.get_esp_distance / vars.set_esp_distance

Включение/отключение отображения дистанции.

---

### vars.get_esp_weapon / vars.set_esp_weapon

Включение/отключение отображения оружия.

---

### vars.get_esp_hp_text / vars.set_esp_hp_text

Включение/отключение текста здоровья.

---

### vars.get_esp_glow / vars.set_esp_glow

Включение/отключение свечения.

---

### vars.get_glow_color / vars.set_glow_color

Цвет свечения.

```lua
vars.set_glow_color(255, 0, 0, 255)
```

---

### vars.get_target_type / vars.set_target_type

Тип цели для аимбота (0 = ближайший, 1 = кроссхейр).

---

### vars.get_bhop / vars.set_bhop

Включение/отключение бхопа.

---

### vars.get_zoom_fov / vars.set_zoom_fov

Включение/отключение зума.

---

### vars.get_zoom_level / vars.set_zoom_level

Уровень зума (1-10).

---

### vars.get_watermark / vars.set_watermark

Включение/отключение водяного знака.

---

### vars.get_crosshair / vars.set_crosshair

Включение/отключение кастомного кроссхейра.

---

### vars.get_crosshair_color / vars.set_crosshair_color

Цвет кроссхейра.

---

### vars.get_crosshair_size / vars.set_crosshair_size

Размер кроссхейра.

---

### vars.get_crosshair_gap / vars.set_crosshair_gap

Зазор кроссхейра.

---

### vars.get_hitsound / vars.set_hitsound

Включение/отключение хитсаунда.

---

### vars.get_hitmarker / vars.set_hitmarker

Включение/отключение хитмаркера.

---

### vars.get_main_color / vars.set_main_color

Основной цвет меню (возвращает/принимает значения 0-255).

```lua
local r, g, b, a = vars.get_main_color()
vars.set_main_color(100, 150, 200, 255)
```

---

## Math (Математика)

Математические функции. Таблица называется `waka_math`.

### waka_math.vec_length

Вычисляет длину вектора.

```lua
local length = waka_math.vec_length(x, y, z)
```

---

### waka_math.vec_distance

Вычисляет расстояние между точками.

```lua
local dist = waka_math.vec_distance(x1, y1, z1, x2, y2, z2)
```

---

### waka_math.normalize_angle

Нормализует угол в диапазон -180 до 180.

```lua
local normalized = waka_math.normalize_angle(angle)
```

---

## Константы

### buttons

Константы кнопок для `cmd`.

| Константа | Описание |
|-----------|----------|
| `buttons.IN_ATTACK` | Основная атака (ЛКМ) |
| `buttons.IN_ATTACK2` | Вторичная атака (ПКМ) |
| `buttons.IN_JUMP` | Прыжок |
| `buttons.IN_DUCK` | Присед |
| `buttons.IN_FORWARD` | Вперед |
| `buttons.IN_BACK` | Назад |
| `buttons.IN_MOVELEFT` | Влево |
| `buttons.IN_MOVERIGHT` | Вправо |
| `buttons.IN_USE` | Использовать |
| `buttons.IN_RELOAD` | Перезарядка |
| `buttons.IN_SPEED` | Бег |
| `buttons.IN_SCOPE` | Прицел |

---

### flags

Флаги состояния игрока.

| Константа | Описание |
|-----------|----------|
| `flags.FL_ONGROUND` | На земле |
| `flags.FL_DUCKING` | Присел |
| `flags.FL_INWATER` | В воде |

---

### keys

Коды виртуальных клавиш.

| Константа | Клавиша |
|-----------|---------|
| `keys.LBUTTON` | Левая кнопка мыши |
| `keys.RBUTTON` | Правая кнопка мыши |
| `keys.MBUTTON` | Средняя кнопка мыши |
| `keys.MOUSE4` | Кнопка мыши 4 |
| `keys.MOUSE5` | Кнопка мыши 5 |
| `keys.SPACE` | Пробел |
| `keys.SHIFT` | Shift |
| `keys.CTRL` | Control |
| `keys.ALT` | Alt |
| `keys.TAB` | Tab |
| `keys.ESCAPE` | Escape |
| `keys.A` - `keys.Z` | Буквы A-Z |
| `keys.0` - `keys.9` | Цифры 0-9 |
| `keys.F1` - `keys.F12` | Функциональные клавиши |

---

## Callbacks (Коллбэки)

### OnPaint

Вызывается каждый кадр для отрисовки.

```lua
function OnPaint()
    -- код отрисовки
end
```

**Важно:** Только здесь работают функции `render.*`

---

### OnCreateMove

Вызывается каждый тик для управления вводом.

```lua
function OnCreateMove()
    -- код управления
end
```

**Важно:** Только здесь работают функции `cmd.*`

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
local box_color = ui.color("box_color", "Box Color", 255, 0, 0, 255)

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
```

### Пример 2: Bunny Hop

```lua
-- Bunny Hop Script
local enabled = ui.checkbox("bhop_enabled", "Enable Bunny Hop", false)
local key = ui.keybind("bhop_key", "Bhop Key", keys.SPACE)

function OnCreateMove()
    if not ui.get("bhop_enabled") then return end
    if not client.is_in_game() then return end
    
    local me = entity.get_local_player()
    if not me or not pawn.is_alive(me) then return end
    
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
```

### Пример 3: Окно с настройками

```lua
-- Settings Window Example
window.create("settings", "My Settings", 100, 100, 300, 200)
window.set_bg_color("settings", 30, 30, 40, 240)
window.set_border_color("settings", 100, 100, 200, 255)
window.set_visible("settings", true)

-- Создаем элементы управления в меню (не в окне!)
local enabled = ui.checkbox("my_feature", "Enable Feature", true)
local slider = ui.slider_float("my_slider", "Value", 50, 0, 100)

function OnPaint()
    if not window.is_visible("settings") then return end
    
    local x, y = window.get_content_pos("settings")
    local w, h = window.get_content_size("settings")
    
    -- Рисуем состояние в окне
    render.text(x, y, "Feature: " .. tostring(ui.get("my_feature")), 255, 255, 255, 255)
    render.text(x, y + 30, "Slider: " .. ui.get("my_slider"), 255, 255, 255, 255)
    
    -- Рисуем прогресс бар
    render.progress_bar(x, y + 60, w - 20, 15, ui.get("my_slider") / 100,
        50, 50, 50, 255, 100, 200, 100, 255, 3)
end
```

---

## ⚠️ Важные замечания

1. **Безопасность указателей**: Все функции автоматически проверяют валидность указателей. Если pawn/controller невалиден, функции вернут нулевые/пустые значения.

2. **Производительность**: Избегайте тяжелых операций в `OnPaint` и `OnCreateMove`, так как они вызываются каждый кадр/тик.

3. **Ошибки скрипта**: Если скрипт вызывает более 10 ошибок подряд, он автоматически отключается.

4. **SEH Protection**: Все функции защищены от вылетов. Краши в Lua скриптах не приведут к падению игры.

5. **Thread Safety**: UI элементы и состояние скриптов защищены мьютексами.

6. **DrawList в OnPaint**: В `OnPaint` доступен DrawList через контекст, не нужно его передавать.

---

## 📝 Лицензия

Этот API предоставляется "как есть" для использования с WAKA CS2.

---

**WAKA Team © 2026**
