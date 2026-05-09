# Vision Indexer v1

Проектная папка для распознавалки и индексатора изображений.

Цель:

- получать от vision-модели не литературное описание, а жёсткий технический JSON;
- нормализовать термины через единый реестр;
- строить гибридный поиск: точный + иерархический + семантический.
- делать базовый семантический матч уже при индексации кадра, а не латать его потом на уровне viewer.

## Главный принцип

Это рабочая архитектура `v1`.
Она не претендует на окончательную форму.
Главный риск системы — не в самой vision-модели, а в дисциплине полей, управлении реестром и метриках качества.

Система делится на 5 слоёв:

1. `Extraction`
   Vision-модель видит картинку и возвращает только структурированные наблюдаемые сигналы.

2. `Normalization`
   Сырые сигналы приводятся к каноническим терминам и иерархиям отдельным детерминированным процессом.

3. `Interpretation`
   Отдельный слой человеческой семантики и сегментации сцены.
   Он не считается физическим truth layer.

4. `Applied Use-Case Layer`
   Прикладная классификация отвечает на вопрос, что именно кадр хорошо показывает в библиотеке.

5. `Retrieval`
   Поиск идёт по фильтрам, реестру, расширениям семейства терминов и семантическому слою.

## Структура папки

```text
vision-indexer-v1/
├── README.md
├── INDEX_SCHEMA.md
├── REGISTRY_MODEL.md
├── NORMALIZER_SPEC.md
├── METRICS.md
├── WORKFLOW.md
├── schemas/
│   └── extraction_output.schema.json
├── prompts/
│   ├── EXTRACTOR_RULES.md
│   └── EXTRACTOR_PROMPT_V1.md
├── classifiers/
│   ├── inferred_style.schema.json
│   ├── applied_use_case.schema.json
│   ├── INFERRED_STYLE_PROMPT_V1.md
│   └── APPLIED_USE_CASE_PROMPT_V1.md
├── registry/
│   └── ...
└── batches/
    ├── inbox/
    │   └── batch-manifest.example.json
    ├── raw-json/
    ├── normalized/
    └── review/
```

## Что считаем успехом v1

- на каждую картинку extraction отдаёт валидный JSON по схеме;
- физические признаки кадра лежат в дискретных полях, а не в prose;
- геометрия, пространственные отношения, резкость, тень и световой рисунок описываются как отдельные технические оси;
- canonical ids появляются только после нормализатора, не внутри vision-ответа;
- поисковые семьи, синонимы и русские поисковые опоры появляются в том же индексном прогоне по кадру;
- любой термин ищется через канон и семейство;
- semantic слой собирается детерминированно, а не генеративно;
- пакет `~25` изображений можно прогонять и ревьюить без ручной пересборки структуры.

## Важная граница

Viewer не должен придумывать базовую семантику.

Если на фото есть плитка, поднос, ключ, линия воды, капля крема, акриловая панель или другой поисково-важный объект, это должно попасть в индекс сразу в момент обработки кадра.

Иными словами:

- extraction обязан увидеть такие сущности;
- indexing обязан сразу развернуть их в поисковые семьи и синонимы;
- viewer потом только использует уже готовые данные.

## Что не входит в v1

- online drift adaptation;
- full multi-object object graph;
- brand-level knowledge graph.

## Боевые артефакты v1

- [schemas/extraction_output.schema.json](/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/research/vision-indexer-v1/schemas/extraction_output.schema.json) — строгая extraction schema
- [prompts/EXTRACTOR_PROMPT_V1.md](/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/research/vision-indexer-v1/prompts/EXTRACTOR_PROMPT_V1.md) — production-style prompt
- [classifiers/inferred_style.schema.json](/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/research/vision-indexer-v1/classifiers/inferred_style.schema.json) — текущая схема для второго слоя сегментации сцены и человеческой семантики
- [classifiers/applied_use_case.schema.json](/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/research/vision-indexer-v1/classifiers/applied_use_case.schema.json) — схема для третьего слоя “чем кадр полезен”
- [batches/inbox/batch-manifest.example.json](/Users/alittlepinkie/Documents/Obsidian Vault/work/Project Folders (codex)/ARCHITECT/research/vision-indexer-v1/batches/inbox/batch-manifest.example.json) — пример manifest для пакетного прогона
