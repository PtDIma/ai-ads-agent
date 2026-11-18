# AI Ads Agent — Project Instructions

Этот файл автоматически загружается Claude Code при работе в папке агента.

## Контекст

Это набор Claude Skills для управления Facebook/Instagram рекламой через MCP сервер `meta-ads`.

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
