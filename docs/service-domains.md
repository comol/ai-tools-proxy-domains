# Домены по сервисам

Проверено **9 октября 2026** по официальной документации и исходникам. Это проверка назначения адресов и покрытия списков, а не проверка доступности через конкретный роутер или прокси. Новые версии и подключённые инструменты могут обращаться к дополнительным адресам.

## Как выбирать список

| Сервис | Файл | Что дополнительно найдено |
| --- | --- | --- |
| Cursor | [`cursor-required-domains.txt`](../hosts/cursor-required-domains.txt) | Региональные Tab/NAL-серверы, текущие адреса входа, hosted computers |
| OpenAI / ChatGPT / Codex | [`openai-domains.txt`](../hosts/openai-domains.txt) | API, авторизация, файлы, CDN, WebSocket, зависимости веб-приложения |
| Claude / Claude Code | [`claude-domains.txt`](../hosts/claude-domains.txt) | Обмен токенов, CDN, обновления, артефакты, MCP и браузерный мост |
| Grok / xAI | [`grok-domains.txt`](../hosts/grok-domains.txt) | API на `x.ai`, вход, пользовательский контент, Grok Build |
| OpenClaw / Hermes | [`agent-integrations.txt`](../hosts/agent-integrations.txt) + файл выбранной модели | Nous Portal, OpenRouter, DeepSeek, ClawHub, Telegram, Discord, веб-поиск |
| Установка и дополнительные библиотеки | [`optional-dependencies.txt`](../hosts/optional-dependencies.txt) | GitHub, npm, PyPI; JS и шрифты для артефактов Claude |

В файлах одна запись на строку; `#` — комментарии. Выбирайте разделы под используемые функции. Записи без `*` — точные имена. `*.example.com` требует правила для поддоменов; в Proxifier эта маска не включает сам `example.com`. У роутеров синтаксис и глубина сопоставления могут отличаться. Не удаляйте `*.` без замены на эквивалентное правило доменного суффикса.

[`exact-split-tunnel.txt`](../hosts/exact-split-tunnel.txt) сохранён как совместимый частичный список только точных имён. Он не заменяет маски из профилей. [`ai-services-masks.txt`](../proxifier/ai-services-masks.txt) остаётся широким вариантом Proxifier и дополнен отсутствовавшими адресами из этих профилей, включая условные интеграции.

## Cursor

Помимо скриншота Required Domains, документация перечисляет `us-asia.gcpp.cursor.sh`, `us-eu.gcpp.cursor.sh`, `us-only.gcpp.cursor.sh`, шесть вариантов `agent`/`agentn` внутри `api5.cursor.sh`, `authenticate.cursor.sh` и `prod.authentication.cursor.sh`. `adminportal42.cursor.sh` нужен для администрирования SSO и доменов. Для hosted computers / Grok Bot приведены обе маски `*.cursorvm.com` и `*.*.cursorvm.com`, поскольку некоторые шлюзы сопоставляют только один уровень поддоменов. [Источник: Network Configuration](https://cursor.com/docs/enterprise/network-configuration).

Для входа дополнительно указаны `accounts.spacex.ai`, `accounts.x.ai`, `auth.x.ai`, `auth.grok.com`, `auth.grokusercontent.com` и `auth.grokipedia.com`. Они включены явно: прежняя маска `*cursor*` их не покрывает. Старые адреса входа сохранены. [Источник: Cursor sign-in domains](https://cursor.com/help/troubleshooting/sign-in-domains).

## OpenAI, ChatGPT и Codex

Для прямого API нужен `api.openai.com`; для ChatGPT/Codex дополнительно учитываются вход, интерфейс, файлы и CDN. В профиле сохранён опубликованный OpenAI список, включая `*.oaistatic.com`, `*.oaiusercontent.com`, `cdn.openaimerge.com` и зависимости WorkOS/Cloudflare. Поддержка, оплата и телеметрия выделены отдельно: весь веб-список не требуется клиенту, использующему только API. [API Reference](https://developers.openai.com/api/reference/overview), [сетевые рекомендации OpenAI](https://help.openai.com/en/articles/9247338-network-recommendations-for-chatgpt-errors-on-web-and-apps).

Для ChatGPT нужны WebSocket-соединения к `ws.chatgpt.com`, для Codex — к `chatgpt.com`, по TCP 443. Прокси должен пропускать `Upgrade: websocket` и длительные соединения. Голос ChatGPT использует UDP 3478; при недоступном UDP возможен TCP 443. Актуальные диапазоны нужно брать из `chatgpt-voice.json`, на который ссылается статья OpenAI: статический снимок IP здесь не добавлен. [Источник](https://help.openai.com/en/articles/9247338-network-recommendations-for-chatgpt-errors-on-web-and-apps).

## Claude

Для Claude Code `platform.claude.com` участвует в обмене и обновлении OAuth-токенов, `downloads.claude.ai` — в установке и обновлениях, `mcp-proxy.anthropic.com` — в подключённых MCP, `bridge.claudeusercontent.com` — в связи с Claude in Chrome. `storage.googleapis.com` нужен для части метаданных плагинов и старых установщиков. При внешнем LLM-провайдере или шлюзе основной адрес запросов определяется конфигурацией. [Источник: Claude Code network configuration](https://code.claude.com/docs/en/network-config).

Для веб-приложения и Desktop добавлены конкретные CDN-хосты, маски `*.claudeusercontent.com`, `*.claudemcpcontent.com`, `*.livepreview.claude.ai` и `*.livepreview.claude.app`. Без них отдельные экраны, артефакты и интерактивные виджеты могут не загрузиться. [Источник: Desktop network requirements](https://code.claude.com/docs/en/desktop#network-access-requirements).

Шрифты и JS-библиотеки некоторых артефактов вынесены в `optional-dependencies.txt`. Это условные зависимости, не обязательные для каждого запроса к модели. [Источник](https://code.claude.com/docs/en/network-config#third-party-hosts-for-artifact-fonts-and-libraries).

## Grok

`api.x.ai` — адрес прямого API; `console.x.ai` — управление ключами, `accounts.x.ai` — учётная запись. Одной маски `*grok.com` недостаточно. Для Grok Build с входом через браузер или device code нужны `cli-chat-proxy.grok.com` и `auth.x.ai`; при прямой API-аутентификации используется `api.x.ai`. [API](https://docs.x.ai/overview), [аккаунты Grok](https://docs.x.ai/grok/faq), [Grok Build network requirements](https://docs.x.ai/build/enterprise).

Маска `*.grokusercontent.com` и дополнительные адреса общего входа подтверждены документацией Cursor. Собственный корпоративный IdP и выбранный внешний способ входа могут требовать других доменов. [Источник](https://cursor.com/help/troubleshooting/sign-in-domains).

## OpenClaw и Hermes

Их список складывается из **провайдера модели + каналов + инструментов**. Например, при выбранном OpenRouter запросы идут к `openrouter.ai`, при прямом DeepSeek — к `api.deepseek.com`. Собственный `base_url` заменяет адрес провайдера; для локальных моделей нужен локальный маршрут. [OpenClaw OpenRouter](https://docs.openclaw.ai/providers/openrouter), [Hermes providers](https://hermes-agent.nousresearch.com/docs/integrations/providers).

Для Hermes с Nous Portal добавлены `portal.nousresearch.com`, `inference-api.nousresearch.com` и `*.nousresearch.com` для управляемых инструментов. Адреса `tool-gateway.nousresearch.com` и `connector-gateway.nousresearch.com` следуют из схемы имён в документации и домена по умолчанию в исходниках; пользовательские переопределения могут их заменить. [Nous Portal](https://hermes-agent.nousresearch.com/docs/integrations/nous-portal), [Tool Gateway](https://hermes-agent.nousresearch.com/docs/user-guide/features/tool-gateway), [исходник, фиксированный коммит](https://github.com/NousResearch/hermes-agent/blob/73162b00eefde3794bed0afb53d84a19c0eed230/tools/managed_tool_gateway.py).

Условные зависимости:

- **ClawHub:** `clawhub.ai` для каталога и установки навыков. [HTTP API](https://docs.openclaw.ai/clawhub/http-api).
- **Telegram:** `api.telegram.org` для Bot API и файлов бота. Ранее добавленные CIDR Telegram относятся к IP-маршрутизации; это не замена доменному правилу Bot API. OpenClaw документирует отдельную настройку прокси канала. [Telegram Bot API](https://core.telegram.org/bots/api#making-requests), [OpenClaw troubleshooting](https://docs.openclaw.ai/channels/telegram/troubleshooting).
- **Discord:** `discord.com`, `gateway.discord.gg`, `cdn.discordapp.com` для REST, WebSocket и вложений. Внешние ссылки во вложениях могут вести на другие хосты. [API/CDN](https://docs.discord.com/developers/reference), [Gateway](https://docs.discord.com/developers/events/gateway).
- **Поиск и извлечение страниц:** `api.search.brave.com`, `api.firecrawl.dev`, `api.exa.ai`, `api.tavily.com` — только для выбранного backend. [OpenClaw web tools](https://docs.openclaw.ai/tools/web), [Brave](https://api-dashboard.search.brave.com/app/documentation/web-search/get-started), [Firecrawl](https://docs.openclaw.ai/tools/firecrawl), [Exa](https://exa.ai/docs/reference/search), [Tavily](https://docs.tavily.com/documentation/api-reference/endpoint/search).
- **Установка и обновления:** GitHub/API/архивы/release assets, npm, PyPI и `openclaw.ai` для установщика. Адреса зависят от способа установки, зеркал и пакетов. [OpenClaw install](https://docs.openclaw.ai/install), [GitHub download hosts](https://docs.github.com/en/actions/reference/runners/self-hosted-runners), [Python package hosts](https://support.claude.com/en/articles/12111783-create-and-edit-files-with-claude).

Браузер, WebFetch, внешние MCP и плагины обращаются к сайтам своих задач. Заранее замкнуть их на перечисленные AI-домены нельзя. Наличие домена в списке само по себе не заставляет фоновый процесс использовать прокси: отдельно проверяйте настройки канала/процесса, DNS, IPv6 и маршрут. `localhost`, локальные шлюзы и LAN сохраняйте в Direct.

## Что не решается списком доменов

- **Потоковые ответы:** нужны длительные соединения, WebSocket и SSE без буферизации. Cursor Tab и некоторые API требуют HTTP/2. [Cursor](https://cursor.com/docs/enterprise/network-configuration), [OpenAI](https://help.openai.com/en/articles/9247338-network-recommendations-for-chatgpt-errors-on-web-and-apps).
- **Сертификаты:** если используется TLS-инспекция, сверяйте совместимость и доверенные CA с документацией клиента; не отключайте проверку сертификатов. [Claude Code](https://code.claude.com/docs/en/network-config).
- **IP-адреса:** CDN и серверные адреса меняются. Для этих AI-сервисов предпочтительны доменные правила, а опубликованные диапазоны специального назначения нужно обновлять по первоисточнику. [Cursor](https://cursor.com/docs/enterprise/network-configuration).

После применения правил проверяйте отдельно вход, текстовый запрос, потоковый ответ, загрузку файла, обновления и используемые интеграции. В Cursor для первичной проверки есть **Run Diagnostic**.
