# AI Ads Agent — Project Instructions

Этот файл автоматически загружается Claude Code при работе в папке агента.

## Контекст

Это набор Claude Skills для управления Facebook/Instagram рекламой через официальный коннектор Meta Ads (`mcp__Meta_ADS__ads_*`). Скиллы писались под старый MCP `meta-ads`; соответствие инструментов в разделе «Meta Ads коннектор» ниже.

## Основные правила работы

1. **Перед любой операцией** — читай `config/ad_accounts.md` и соответствующий бриф из `config/briefs/`
2. **Перед изменениями в кабинете** — показывай план и жди подтверждения пользователя
3. **Опасные операции** (изменение бюджетов, пауза/возобновление, создание кампаний) — ВСЕГДА требуют подтверждения
4. **После выполнения действий** — логируй их в `history/YYYY-MM/YYYY-MM-DD.md`
5. **Перед оптимизацией** — читай историю действий за последние 3 дня

## Skills

Все skills находятся в `skills/`. Главный — `/ads-agent` (точка входа). Подробное описание каждого skill в его `SKILL.md`.

Девять основных skills (ads-agent, ads-optimizer, ads-reporter, campaign-manager, targeting-expert, creative-copywriter, creative-analyzer, creative-image-generator, dashboard) установлены как skills аккаунта claude.ai. Оставшиеся три (`naming-rules`, `account-onboarding`, `account-delete`) подключены как project skills в `.claude/skills/` и загружаются автоматически в этой папке. Не копируй первые девять в `.claude/skills/`, чтобы не было дублей.

Дополнительно в `.claude/skills/` есть аналитические Meta-скиллы из [marketing-skills](https://github.com/irinabuht12-oss/marketing-skills) с префиксом `meta-`: `meta-ads-audit`, `meta-creative-fatigue-detection`, `meta-audience-overlap-analysis`, `meta-frequency-cap-recommendations`, `meta-retargeting-window-analysis`, `meta-competitor-creative-analysis`. Они только анализируют и дают рекомендации; изменения в кабинете идут через основные skills с подтверждением.

## Пути к файлам

Все данные лежат в корне репозитория. Если skill ссылается на старые пути, используй соответствие:

| Путь в skill | Реальный путь |
|---|---|
| `.claude/ads-agent/config/...` | `config/...` |
| `.claude/ads-agent/knowledge/...` и `knowledge/...` | `config/knowledge/...` |
| `.claude/ads-agent/history/...` | `history/...` |

Правила нейминга аккаунтов (`/naming-rules`) сохраняются в `config/naming/{account}_naming.md`.

## База знаний

Перед принятием решений изучи:
- `config/knowledge/safety_rules.md` — правила безопасности и лимиты
- `config/knowledge/metrics_glossary.md` — формулы и интерпретация метрик
- `config/knowledge/fb_best_practices.md` — best practices Facebook Ads
- `config/knowledge/troubleshooting.md` — решения типичных проблем

## Полная инструкция агента

Подробные правила работы (форматы логов, структура конфигурации, типы операций) — в `config/AGENT.md`.

## Meta Ads коннектор: соответствие инструментов

Скиллы писались под кастомный MCP `meta-ads` (`get_campaigns`, `get_insights`, `pause_ad` и т.д.). Сейчас подключён официальный коннектор Meta Ads, его инструменты называются `mcp__Meta_ADS__ads_*`. Девять скиллов аккаунта claude.ai (ads-agent, ads-optimizer, ads-reporter, campaign-manager, targeting-expert, creative-copywriter, creative-analyzer, creative-image-generator, dashboard) и `config/AGENT.md` по-прежнему ссылаются на старые имена. **Когда скилл называет старый инструмент, вызывай его аналог из таблицы ниже.** Если инструменты коннектора не видны, загрузи их через ToolSearch (`select:mcp__Meta_ADS__ads_get_ad_entities,...`).

### Общие правила коннектора

- `ad_account_id` передаётся числом **без** префикса `act_` (например `1621311852554891`).
- В каждый вызов передавай `client_conversation_id` (20 символов A-Za-z0-9, один на разговор; дальше берётся из ответа) и `advertiser_job` (`report_measure_or_look_up` для отчётов, `assess_and_optimize_performance` для оптимизации, `create_and_launch_new_ads` для создания и т.д.).
- Перед запросом метрик проверь имена полей через `ads_get_field_context`. Старые имена переименованы: `spend` → `amount_spent`, `actions:lead` → `lead`, лиды/покупки → `results` + `cost_per_result`.
- Бюджеты в центах валюты кабинета (USD): $50/день = `5000`. Минимум в кабинетах: `min_daily_budget_cents` = 100.
- **Draft mode включён.** Создание и правки (кроме чистой паузы) попадают в черновик Ads Manager и не идут в эфир, пока не вызван `ads_activate_entity`. Пауза `{"status":"PAUSED"}` применяется сразу. После паузы НЕ вызывай `ads_activate_entity`, это может её отменить.
- Правила подтверждения из этого файла действуют как раньше: всё, что меняет кабинет (`ads_update_entity`, `ads_activate_entity`, `ads_create_*`, `ads_duplicate_entity`, `ads_*_custom_audience`, загрузка медиа), только после явного «да» пользователя и с записью в `history/`.

### Таблица соответствия

| Старый инструмент (`meta-ads`) | Инструмент коннектора | Как вызывать |
|---|---|---|
| `get_ad_accounts`, `get_account_info` | `ads_get_ad_accounts` | Возвращает id, имя, валюту, статус, `is_queryable`, `min_daily_budget_cents`. Метрики уровня аккаунта: `ads_get_ad_entities(level="ad_account")` |
| `get_campaigns(account_id, limit)` | `ads_get_ad_entities` | `level="campaign"`, `fields=["id","name","status","effective_status","daily_budget","objective"]`, `limit` |
| `get_adsets(campaign_id)` | `ads_get_ad_entities` | `level="adset"`, `filtering=[{"field":"campaign.id","operator":"IN","value":["<id>"]}]` |
| `get_adset_details(adset_id)` | `ads_get_ad_entities` | `level="adset"`, `object_ids=["<id>"]`, поля `targeting`, `daily_budget`, `optimization_goal`, `learning_stage_info`, `frequency_control_specs` |
| `get_ads(adset_id)` | `ads_get_ad_entities` | `level="ad"`, фильтр `campaign.id IN [...]` или `adset.id IN [...]` (имя поля фильтра сверяй через `ads_get_field_context`) |
| `get_insights(object_id, time_range, level)` | `ads_get_ad_entities` | Метрики сразу в `fields` (`amount_spent`, `impressions`, `reach`, `frequency`, `clicks`, `ctr`, `cpc`, `cpm`, `results`, `cost_per_result`, `lead`, `cost_per_lead`, `purchase_roas`). Период: `date_preset` (`today`, `yesterday`, `last_3d`, `last_7d`, `last_14d`, `last_30d`, `this_month`…) или `time_range='{"since":"YYYY-MM-DD","until":"YYYY-MM-DD"}'`, не оба сразу. По дням: `time_increment="1"`. Разбивки: `breakdowns` (`age`, `gender`, `publisher_platform`…). Для сравнения 5 периодов делай 5 вызовов |
| `get_insights` для трендов/выгорания | `ads_insights_performance_trend`, `ads_insights_anomaly_signal` | Тренд CPC/CPM/CPR/CTR/ROAS по всей истории (без дат), аномалии и резкие скачки. Сначала читай сущности через `ads_get_ad_entities` |
| `pause_campaign` / `pause_adset` / `pause_ad` | `ads_update_entity` | `entity_type` = `campaign` / `ad_set` / `ad`, `entity_id`, `fields='{"status":"PAUSED"}'` |
| `resume_campaign` / `resume_adset` / `resume_ad` | `ads_activate_entity` | `entity_type`, `entity_id`. Активирует только сам объект, родители должны быть ACTIVE. Также публикует черновики |
| `update_adset(budget)` | `ads_update_entity` | `entity_type="ad_set"`, `fields='{"daily_budget":5000}'` (центы). Для CBO бюджет на `entity_type="campaign"`. В draft mode правка уходит в черновик, публикация через `ads_activate_entity` |
| `update_adset(targeting)`, `update_targeting` | `ads_update_entity` | `fields='{"targeting":{...}}'` заменяет весь targeting: сначала прочитай текущий и сохрани `targeting_automation` |
| `create_campaign` | `ads_create_campaign` | `campaign_name`, `objective` только ODAX (`OUTCOME_LEADS`, `OUTCOME_SALES`, `OUTCOME_TRAFFIC`, `OUTCOME_ENGAGEMENT`, `OUTCOME_AWARENESS`, `OUTCOME_APP_PROMOTION`), `buying_type="AUCTION"`, бюджет CBO в `campaign_daily_budget` |
| `create_adset` | `ads_create_ad_set` | `campaign_id`, `ad_set_name`, `billing_event="IMPRESSIONS"`, `optimization_goal`, `targeting` (JSON-строка), `daily_budget` только для ABO, `promoted_object` для лидов/конверсий |
| `create_ad` | `ads_create_ad` | `ad_set_id`, `ad_name`, `creative='{"creative_id":"<id>"}'` |
| `create_ad_creative`, `create_video_creative` | `ads_create_creative` | `page_id`, `link_url`, `image_hash` или `video_id`, `message`/`primary_texts`, `headline`, `call_to_action_type`. Лид-форма: `lead_gen_form_id`. WhatsApp: `call_to_action_type="WHATSAPP_MESSAGE"`, `link_url="https://api.whatsapp.com/send"` |
| `create_*_carousel`, `create_website_carousel` | `ads_create_creative` | `cards=[{image_hash, link, headline, description}, ...]` (2–10 карточек) |
| `get_ad_creatives`, `get_creative_tag` | `ads_get_creatives` | `ad_account_id` или `creative_ids`. Связь объявление→креатив: поле `creative_id` в `ads_get_ad_entities(level="ad")`. Превью: `ads_get_ad_preview` |
| `upload_ad_image` | `ads_creative_upload_media` | `upload_source="URL"`, `media_type="IMAGE"`, `media_url`. Для локального файла: `ads_create_upload_url` → загрузка → `ads_finalize_upload` |
| `upload_video` | `ads_creative_upload_media` | `media_type="VIDEO"`. Статус обработки: `ads_get_ad_videos(video_ids=[...])` |
| `get_ad_image`, `get_video_status` | `ads_get_ad_images(hashes=[...])`, `ads_get_ad_videos(video_ids=[...])` | |
| `get_custom_audiences` | `ads_get_ad_account_custom_audiences` | `subtype_filter` опционально. Одна аудитория: `ads_get_custom_audience` |
| `create_lookalike_audience` | `ads_create_custom_audience` | `subtype="LOOKALIKE"`, `origin_audience_id`, `lookalike_ratio` 0.01–0.20. Страну не передавать |
| (кастомные аудитории сайта/вовлечённости) | `ads_create_custom_audience` | `subtype` = `WEBSITE` / `ENGAGEMENT` / `CUSTOM`, `rule` JSON-строкой |
| `get_account_pages` | `ads_get_ad_account_pages` | Инстаграм-аккаунты: `ads_get_ig_accounts` |
| дублирование кампании/адсета/объявления | `ads_duplicate_entity` | `entity_type`, `copy_requests=[{"entity_id":"..."}]` |
| (рекомендации по аккаунту) | `ads_get_opportunity_score` | Opportunity Score и рекомендации Meta |
| (ошибки доставки, история изменений) | `ads_get_errors`, `ads_account_get_activity_logs` | |
| поиск конкурентов в Ad Library | `ads_library_search` | `search_terms`, `page_ids`, `countries` (ISO-2) |

### Чего в коннекторе нет

| Старый инструмент | Что делать |
|---|---|
| `search_interests`, `get_interest_suggestions`, `search_demographics` | Поиска интересов нет. Используй ID интересов, уже сохранённые в брифе или в `targeting` существующих адсетов (`ads_get_ad_entities`, поле `targeting`), либо попроси пользователя взять их из Ads Manager. Не выдумывай ID. По умолчанию Meta рекомендует широкий таргетинг + Advantage+ Audience |
| `search_geo_locations` | Поиска гео нет. Бери ключи из `config/knowledge/geo_locations.md` или из `targeting` существующих адсетов; страны и регионы задаются кодами (`countries: ["UA"]`). Если города нет нигде, попроси ключ у пользователя |
| `estimate_audience_size` | Оценки охвата нет. Смотри `reach` по похожим адсетам или проверяй в Ads Manager |
| `generate_creative_image` (Gemini) | Генерации картинок с нуля в коннекторе нет. `ads_creative_create_image_variations` делает только вариации существующей картинки кабинета (нужен `seed_image_hash`). Для генерации с нуля скиллу `creative-image-generator` нужен свой `GEMINI_API_KEY` |
| `get_login_link` | Не нужен: авторизация коннектора идёт через claude.ai |
