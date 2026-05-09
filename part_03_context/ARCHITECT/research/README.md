# 📸 Photo Indexing System

Система для индексации фотографий с каноническим реестром тегов, семантическим поиском и человеческими описаниями.

## Ключевые принципы

### Проблема синонимов — решена через реестр

Без реестра: фото с водой ищется по `"вода"`, другое — только по `"брызги"`. Это плохо.

С реестром: и `"вода"`, и `"брызги"`, и `"splash"`, и `"струя"` — всё приводится к canonical тегу `water`. Обе фотографии находятся по любому из этих слов.

---

## Быстрый старт

```bash
# 1. Установить зависимости
pip install gallery-dl clip-interrogator ultralytics clip-retrieval pyyaml

# 2. Установить ollama + llava
# https://ollama.com → скачать и запустить
ollama pull llava

# 3. Скачать фото
./scripts/download.sh "https://www.behance.net/gallery/12345/Project" ./photos

# 4. Описать фото (LLaVA)
./scripts/describe.sh ./photos/

# 5. Извлечь теги (CLIP + YOLO)
./scripts/tag.sh ./photos/

# 6. Собрать индекс
./scripts/index.sh ./photos/ ./index/

# 7. Искать
./scripts/search.sh "брызги воды"
./scripts/search.sh "product minimal cold tone" --top 5
```

---

## Архитектура

### Три слоя индекса для каждого фото

```
┌─────────────────────────────────────────────────────────────┐
│  СЛОЙ 1: Технические теги (checkboxes)                      │
│  lighting / composition / mood / color / format / style      │
├─────────────────────────────────────────────────────────────┤
│  СЛОЙ 2: Объекты (main_subject / foreground / background)   │
│  Автоматически через YOLO + нормализация через реестр        │
├─────────────────────────────────────────────────────────────┤
│  СЛОЙ 3: Человеческое описание (LLaVA)                      │
│  Развёрнутый текст → CLIP эмбеддинг для семантического поиска│
└─────────────────────────────────────────────────────────────┘
```

### Технические теги — словарь значений

| Категория | Значения |
|-----------|----------|
| `lighting` | `backlight`, `studio_light`, `natural_light`, `golden_hour`, `harsh_light` |
| `composition` | `rule_of_thirds`, `center_composition`, `minimal_composition`, `flat_lay`, `close_up`, `diagonal` |
| `mood` | `calm_mood`, `energetic_mood`, `dramatic_mood`, `soft_mood`, `dark_mood`, `fresh_mood` |
| `color` | `warm_tone`, `cold_tone`, `neutral_tone`, `monochrome`, `vibrant`, `pastel`, `dark_tone`, `light_tone` |
| `format` | `vertical`, `horizontal`, `square` |
| `style` | `product_photo`, `lifestyle`, `editorial`, `abstract`, `portrait` |

---

## Формат итогового JSON

```json
{
  "file": "photos/photo_001.jpg",
  "source": "behance.net/gallery/12345",
  "description": "Рекламная съёмка продукта на белом мраморном фоне с динамичной струёй воды. Вертикальный кадр, холодный тон, ощущение свежести и чистоты.",
  "tags_canonical": ["water", "product_photo", "marble", "vertical", "cold_tone", "backlight", "fresh_mood"],
  "tags_technical": {
    "lighting": ["backlight"],
    "composition": ["center_composition"],
    "mood": ["fresh_mood", "energetic_mood"],
    "color": ["cold_tone"],
    "format": ["vertical"],
    "style": ["product_photo"]
  },
  "objects": {
    "main_subject": ["bottle"],
    "foreground": ["water"],
    "background": ["marble", "white_background"],
    "props": ["leaf"]
  }
}
```

---

## Реестр тегов (`tag-registry.yaml`)

### Принцип работы

Каждое понятие имеет **один canonical тег** + список aliases на RU и EN.

```yaml
water:
  canonical: "water"
  category: "element"
  aliases:
    - вода
    - брызги       ← все эти слова → canonical "water"
    - струя
    - капли
    - splash
    - drops
```

### Правила добавления тегов

1. **Нет нового тега для синонима** — добавь alias к существующему
2. **Новый тег только для нового понятия** — если ни один existing canonical не подходит
3. **Canonical всегда на английском**, алиасы — RU + EN
4. **Одна category** — `element / material / lighting / color / composition / format / style / mood / object / background`

### Как добавить новый тег

```yaml
# Добавить в tag-registry.yaml:
perfume:
  canonical: "perfume"
  category: "object"
  aliases:
    - духи
    - парфюм
    - fragrance
    - аромат
    - cologne
    - одеколон
```

---

## Скрипты

### `scripts/download.sh` — Скачать фото

```bash
./scripts/download.sh <URL> [output_dir]

# Примеры:
./scripts/download.sh "https://www.behance.net/gallery/12345/Name"
./scripts/download.sh "https://500px.com/p/username" ./photos
./scripts/download.sh "https://www.flickr.com/photos/user/sets/123"
```

Поддерживаемые источники: Behance, 500px, Flickr, Instagram (публичные), прямые URL изображений и [все источники gallery-dl](https://github.com/mikf/gallery-dl/blob/master/docs/supportedsites.md).

### `scripts/describe.sh` — Описать фото через LLaVA

```bash
./scripts/describe.sh <photo.jpg | ./photos/>
```

Генерирует `.description.json` рядом с каждым фото:
```json
{
  "description": "Рекламная съёмка...",
  "objects_raw": ["bottle", "water", "marble"],
  "mood_raw": ["fresh", "energetic"]
}
```

### `scripts/tag.sh` — Извлечь и нормализовать теги

```bash
./scripts/tag.sh <photo.jpg | ./photos/> [registry.yaml]
```

Генерирует `.tags.json` с нормализованными тегами через реестр.

### `scripts/index.sh` — Собрать финальный индекс

```bash
./scripts/index.sh <photos_dir> [index_dir]
```

Объединяет description + tags → `index/metadata.jsonl` + CLIP FAISS индекс.

### `scripts/search.sh` — Поиск

```bash
./scripts/search.sh "запрос" [--top N] [--mode tags|semantic|combined] [--index path]

# Примеры:
./scripts/search.sh "вода"                          # найдёт и "брызги", и "струя"
./scripts/search.sh "product cold tone"
./scripts/search.sh "драматичный свет" --top 3
./scripts/search.sh "minimal flat lay" --mode semantic
```

**Режимы поиска:**
- `tags` — точный поиск по canonical тегам через реестр
- `semantic` — семантический поиск по CLIP эмбеддингам
- `combined` (по умолчанию) — оба режима + ранжирование

---

## Стек инструментов

| Задача | Инструмент | Установка |
|--------|-----------|-----------|
| Скачать фото | `gallery-dl` | `pip install gallery-dl` |
| Описание + объекты | `ollama` + LLaVA | [ollama.com](https://ollama.com) |
| Технические теги | `clip-interrogator` | `pip install clip-interrogator` |
| Объекты на фото | `ultralytics YOLO` | `pip install ultralytics` |
| CLIP FAISS индекс | `clip-retrieval` | `pip install clip-retrieval` |
| Реестр тегов | YAML + Python | `pip install pyyaml` |

---

## Структура файлов

```
research/
├── tag-registry.yaml          ← Единый реестр тегов (редактировать здесь)
├── scripts/
│   ├── download.sh            ← Скачать фото из URL
│   ├── describe.sh            ← Описать через LLaVA
│   ├── tag.sh                 ← Извлечь теги (CLIP + YOLO)
│   ├── index.sh               ← Собрать индекс
│   └── search.sh              ← Поиск
├── photos/                    ← Скачанные фото
│   ├── photo_001.jpg
│   ├── photo_001.description.json
│   ├── photo_001.tags.json
│   └── ...
└── index/
    ├── metadata.jsonl         ← Все метаданные (1 JSON на строку)
    ├── index_summary.json     ← Сводный индекс
    └── clip_index/            ← FAISS файлы для семантического поиска
```
