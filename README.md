# Sofle Choc Wireless

Personal ZMK keymap and config for a wireless split Sofle Choc on the proXiao (Seeed XIAO BLE) shield.

Личная раскладка и конфигурация [ZMK](https://zmk.dev/) для беспроводной split-клавиатуры Sofle на свитчах Choc. Контроллер — Seeed XIAO BLE, шилд `proXiao`: в системе клавиатура называется **Sofle**.

![Sofle 2 1 proger](https://github.com/DmNep/proXiao/assets/133882902/29a600f7-c39b-4701-a967-3ded3b045146)

Ветка по-прежнему называется `corne`: под этим именем она была в upstream, а в репозитории на ней конфигурация Sofle.

## Что изменено относительно шилда

Кеймап — `config/boards/shields/proXiao/proXiao.keymap`. Имя, сон и Bluetooth — `config/proXiao.conf`.

Верхний ряд базового слоя отдан отладке. На левой половине:

| Короткое нажатие | Удержание |
| --- | --- |
| Esc | F1 |
| F2 | F12 |
| F5 | F7 |
| F9, F10, F11 | — |

F3 и Shift+F3 крутит левый энкодер. F4 назначен как Alt+F4 (двойное нажатие Alt). Вместе это F1–F3, F5, F7, F9–F12 и Alt+F4.

Справа в том же ряду: Print Screen, Caps Word, комментарий (Ctrl+\\), снять комментарий (Ctrl+Shift+\\), Delete, Page Up.

Дальше по базовому слою:

- QWERTY. Тап-дансы: T / ё (Grave), B / Q, I / O, M / `]`.
- Липкие Ctrl и Shift с быстрым сбросом. Enter после себя включает липкий Shift. Правый нижний мизинец — Ctrl+Enter.
- Язык: большой палец шлёт Ctrl+Shift+8 (RU). Комбо этой клавиши с пробелом — Ctrl+Shift+9 (EN).
- Знаки под русскую раскладку Windows набраны Alt-кодами (№, кавычки, скобки, `@`, `#`, `%` и соседние). Запятая и точка с пробелом — отдельные макросы.
- Комбо: Backspace+Shift — Shift+Delete; Shift+Enter — `ro`.
- Подсветка и экран в конфиге выключены (`CONFIG_ZMK_RGB_UNDERGLOW`, `CONFIG_ZMK_DISPLAY`). Сон через 15 минут простоя. До 6 сопряжённых BLE-устройств, отчёт о батарее раз в 120 секунд. Энкодеры EC11 включены на обеих половинках. Переключение Bluetooth-профилей на клавиши не выведено.

## Слои

Номера — порядок слоёв в кеймапе.

1. **Lower** — удержание мизинцев. Стрелки, цифровой блок, Home/End, переключатели слоёв 2–4. Bootloader — клавиши нижнего ряда у внутреннего края каждой половинки. Левый энкодер: громкость. Правый: Page Up / Page Down.
2. **Raise** — переключатель с Lower. Навигация, копировать и вставить, комментарии, Ctrl+S, Ctrl+Enter. Выключается второй клавишей верхнего ряда этого слоя.
3. **Dota 2** — переключатель с Lower. QWER, цифры 2–6, на больших пальцах Y, U, I, O, плюс Ctrl, Shift, Alt и Mute. Выключается второй клавишей верхнего ряда.
4. **Dota 2, Arc** — переключатель с Lower. Короткие имена предметов: `gao`, `gs`, `gd`, `gf`, `g6`, `go`, `bao`, `bs`, `bd`, `bn`, `b6`, `bo`. Выключается второй клавишей верхнего ряда.
5. **Окна** — удержание клавиши GUI на левом большом пальце. Win+1…6, Win+Shift+1…6, Win+стрелки, Win+Tab, Win+E, Win+D, Win+L, Win+V, Win+I.

На базовом слое правый энкодер меняет громкость.

## Сборка и прошивка

Прошивку собирает GitHub Actions.

[`.github/workflows/build.yml`](.github/workflows/build.yml) запускается на `push`, `pull_request` и вручную (`workflow_dispatch`) и вызывает [`zmkfirmware/zmk/.github/workflows/build-user-config.yml@main`](https://github.com/zmkfirmware/zmk/blob/main/.github/workflows/build-user-config.yml). Ревизия ZMK — `main` в [`config/west.yml`](config/west.yml). Матрица — [`build.yaml`](build.yaml): две половинки шилда `proXiao` на плате `seeeduino_xiao_ble` (`proXiao_left` — central, `proXiao_right`). Тот же идентификатор платы записан в `config/boards/shields/proXiao/proXiao.zmk.yml`.

На текущей ветке `main` ZMK этот контроллер переименован в `xiao_ble//zmk` ([заметка про Zephyr 4.1](https://zmk.dev/blog/2025/12/09/zephyr-4-1)). Если сборка на `main` не принимает `seeeduino_xiao_ble`, в `build.yaml` нужна эта новая форма.

После зелёного прогона во вкладке Actions скачивается архив `firmware`. Внутри два UF2, по одному на половинку: `proXiao_left-seeeduino_xiao_ble-zmk.uf2` и `proXiao_right-seeeduino_xiao_ble-zmk.uf2`. Имя файла — `{shield}-{board}-zmk.uf2`, символ `/` в идентификаторе платы заменяется на `_`.

Прошивка Seeed XIAO BLE:

1. Дважды быстро нажать RESET на нужной половинке. Появится USB-диск загрузчика. То же делает клавиша bootloader на нижнем слое.
2. Скопировать UF2 этой половинки в корень диска. Диск отмонтируется сам, контроллер перезагрузится.
3. Прошить обе половинки. Затем сбросить их одновременно: через несколько секунд половины спариваются по BLE. Левая — central.
4. В списке Bluetooth хоста имя — **Sofle**. Сначала имеет смысл проверить клавиатуру по USB.

Имя в `config/proXiao.conf`:

```
CONFIG_ZMK_KEYBOARD_NAME="Sofle"
```

## Авторство и лицензия

Основано на [aroum/proXiao](https://github.com/aroum/proXiao).

Файл [`LICENSE`](LICENSE) — MIT, copyright (c) 2022 aroum. Кеймап и описание шилда дополнительно помечены `SPDX-License-Identifier: MIT`, copyright (c) 2020 The ZMK Contributors.
