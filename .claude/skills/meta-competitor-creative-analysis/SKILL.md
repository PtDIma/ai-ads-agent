---
name: meta-competitor-creative-analysis
description: Pulls competitor ads from Meta Ad Library and Google Ads Transparency Center, categorizes their messaging angles, formats, CTAs, and creative types, then identifies gaps in their approach you can exploit and patterns worth testing in your own campaigns. Platform: Meta.
metadata:
  platform: Meta
---

# 13/ Competitor Creative Analysis — Meta

## What it does
Pulls competitor ads from Meta Ad Library and Google Ads Transparency Center, categorizes their messaging angles, formats, CTAs, and creative types, then identifies gaps in their approach you can exploit and patterns worth testing in your own campaigns.

## How it works
Claude reviews competitor ad libraries and breaks down what they're running — how many active ads, what formats (video, static, carousel), what messaging themes (pain point, benefit, social proof, urgency), how often they rotate, and what offers they're pushing. It then maps this against your current creative strategy to find differentiation opportunities.

## Practical example
Your three main SaaS competitors are all running 15-25 active ads each on Meta. Claude finds that all three lead with feature-comparison messaging and "book a demo" CTAs. None are using customer testimonials as primary creative, none are running educational content ads, and only one uses video. Claude recommends testing UGC testimonial ads with a free trial CTA — a combination no competitor is using — and flags that Competitor B just launched 8 new ads in the last week, suggesting a campaign refresh or new product push.

## What you get back
- Competitor ad inventory breakdown by format, messaging theme, and CTA type
- Creative rotation frequency showing how often competitors refresh
- Messaging gaps no competitor is filling that you can own
- Patterns across competitors that suggest industry conventions worth testing or deliberately breaking
- Timeline of competitor ad launches to track their strategy shifts over time

## When to use it
- Quarterly competitive reviews to stay ahead of messaging shifts
- Before creative brainstorms to identify white space in the market
- When entering a new market or vertical and you need to understand the landscape fast
- When your CPA rises and you suspect competitors are getting more aggressive

## Data access

В этом проекте данные берутся из коннектора Meta Ads (`mcp__Meta_ADS__ads_*`): сущности и метрики через `ads_get_ad_entities` (имена полей сверяй в `ads_get_field_context`), тренды через `ads_insights_performance_trend`, аномалии через `ads_insights_anomaly_signal`. Полное соответствие старых инструментов новым в `CLAUDE.md`. Объявления конкурентов из Meta Ad Library получай через `ads_library_search` (`search_terms`, `page_ids`, `countries` в ISO-2, `ad_active_status="ACTIVE"`). Перед анализом прочитай `config/ad_accounts.md` и бриф аккаунта из `config/briefs/`, правила из `config/knowledge/safety_rules.md`. Скилл только анализирует и рекомендует: любые изменения в кабинете (пауза, бюджеты, исключения аудиторий) делаются через `/ads-agent` или `/campaign-manager` с подтверждением пользователя и логом в `history/`.

_Источник: [irinabuht12-oss/marketing-skills](https://github.com/irinabuht12-oss/marketing-skills) (MIT), адаптировано под этот проект._
