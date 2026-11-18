# Ad Accounts

Список рекламных аккаунтов, которыми управляет агент.

---

## Как добавить аккаунт

1. Скопируй блок-шаблон ниже и заполни его данными своего аккаунта
2. Создай бриф в `briefs/{account_name}.md` (используй `briefs/_template.md`)
3. Убедись, что System User Token имеет доступ к этому аккаунту

---

## Аккаунт 1: {Название проекта}

- **Account ID**: act_XXXXXXXXXX
- **Page ID**: XXXXXXXXXXXXXX
- **Instagram ID**: XXXXXXXXXXXXXX (или "нет")
- **Название**: {Название бизнеса в Facebook}
- **Сайт**: https://example.com
- **Бриф**: [briefs/{filename}.md](briefs/{filename}.md)
- **Статус**: активен | приостановлен | архив
- **Валюта**: USD | EUR | UAH | ...
- **Часовой пояс**: UTC+X ({Город})
- **Тип конверсии**: Lead-формы | WhatsApp | Site Leads (Pixel) | Engagement
- **Заметки**: {Краткое описание ниши, особенности, ограничения}

---

## Аккаунт 2: Кабинет 2 (Кристина Лисаковская)

- **Account ID**: act_2672992426390352
- **Page ID**: 997667263410443 (Кристина Лисаковская)
- **Instagram ID**: _уточнить_
- **Название**: Кабинет 2
- **Бизнес-менеджер**: Kristina Lysakivska
- **Бриф**: [briefs/kabinet_2.md](briefs/kabinet_2.md)
- **Статус**: активен (новая кампания в подготовке)
- **Валюта**: USD
- **Часовой пояс**: _уточнить_
- **Тип конверсии**: _уточнить_
- **Заметки**: Старые кампании (`ua_bs_h_mini…`, OUTCOME_SALES, custom conversion «Оплата тест») остановлены и не учитываются. Запускаем новую кампанию с нуля.

---

## Где найти ID

### Account ID
1. Facebook Ads Manager → Настройки аккаунта
2. Или в URL: `https://adsmanager.facebook.com/adsmanager/manage/accounts?act=XXXXXXXXX`
3. Или через MCP: `get_ad_accounts()`

### Page ID
1. Facebook Page → О странице → Прозрачность страницы
2. Или: https://findmyfbid.in/

### Instagram ID
1. Через MCP: `get_account_pages(account_id)` вернёт `instagram_accounts`
2. Или в Business Suite: Instagram → Настройки

---

## Статусы аккаунтов

| Статус | Описание |
|---|---|
| **активен** | Аккаунт в работе, можно оптимизировать |
| **приостановлен** | Временно не работаем, не трогать |
| **архив** | Не используется, только для истории |
