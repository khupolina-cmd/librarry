# Font Library

Локальная HTML-библиотека шрифтов с живыми превью.

## Быстрый старт

Откройте библиотеку через локальный сервер (рекомендуется), чтобы корректно загружались локальные шрифты и `fonts.json`:

```bash
# Python 3
python -m http.server 8080

# Node.js (npx)
npx serve .

# PHP
php -S localhost:8080
```

Затем откройте [http://localhost:8080/font-library/](http://localhost:8080/font-library/) в браузере.

> **Примечание:** Открытие `index.html` двойным кликом (`file://`) может блокировать загрузку ресурсов в браузере. Используйте локальный сервер.

---

## Добавить свой шрифт

### 1. Поместите файлы шрифта

```
font-library/
  fonts/
    your-font-name/
      YourFont-Regular.woff2
      YourFont-Bold.woff2
      YourFont-Italic.woff2
```

> Рекомендуемый формат: **woff2** (компактный, поддерживается всеми браузерами).
> Исходники otf/ttf можно хранить рядом отдельно.

### 2. Добавьте запись в `fonts.json`

```json
{
  "id": "your-font-id",
  "family": "Your Font",
  "google_fonts_id": null,
  "google_fonts_variants": null,
  "local_path": "fonts/your-font-name/",
  "files": [
    { "file": "fonts/your-font-name/YourFont-Regular.woff2", "weight": 400, "italic": false },
    { "file": "fonts/your-font-name/YourFont-Bold.woff2",    "weight": 700, "italic": false },
    { "file": "fonts/your-font-name/YourFont-Italic.woff2",  "weight": 400, "italic": true  }
  ],
  "default_weight": 400,
  "weight_range": [400, 700],
  "tags": {
    "style":    ["serif", "contemporary"],
    "mood":     ["чувственный", "современный"],
    "function": ["headline", "display"],
    "context":  ["beauty", "fashion"]
  },
  "technical": {
    "has_cyrillic":  true,
    "is_variable":   false,
    "styles_count":  3,
    "license_status": "checked_private_local",
    "license_note":  "Desktop license, private local use only"
  },
  "description": "Короткое описание характера шрифта.",
  "specimen": {
    "hero":   "ВАШИ\nСЛОВА",
    "poster": "Строка для постера\nи ритм композиции",
    "info":   "Технический текст · Спецификация · Упаковка"
  },
  "index": {
    "style_label":    "contemporary editorial serif",
    "mood_label":     "чувственный, современный",
    "function_label": "hero-заголовок, display",
    "context_label":  "beauty, fashion",
    "strength":       "За что брать этот шрифт"
  }
}
```

---

## Поля `license_status`

| Значение               | Описание                                              |
|------------------------|-------------------------------------------------------|
| `web_use_allowed`      | OFL, Apache 2.0 или иная свободная веб-лицензия       |
| `checked_private_local`| Desktop-лицензия, только локальное частное применение |
| `unknown`              | Статус лицензии не проверен                           |

> **Важно:** Desktop-лицензия ≠ webfont-лицензия. Не публикуйте эту библиотеку как публичный сайт с коммерческими шрифтами без webfont-лицензии.

---

## Структура

```
font-library/
  index.html       ← главная страница (всё встроено: CSS + JS)
  fonts.json       ← метаданные шрифтов
  fonts/           ← папка для файлов шрифтов
    your-font/
      YourFont-Regular.woff2
  README.md        ← этот файл
```

---

## Демо-режим

В `fonts.json` по умолчанию заданы шрифты с Google Fonts CDN (`google_fonts_id`).  
Библиотека автоматически загружает их при наличии интернета.  
Когда вы добавляете локальные файлы в `files[]`, CDN перестаёт использоваться — приоритет всегда у локальных файлов.
