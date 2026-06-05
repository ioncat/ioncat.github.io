# Career Agent — Product Brief

**Версия:** июнь 2026  
**Автор:** Oleksii Bondarenko · [LinkedIn](https://www.linkedin.com/in/alexibondarenko/) · [ioncat.github.io](https://ioncat.github.io)

---

## Проблема и рынок

PM-рынок конкурентный и становится ещё конкурентнее: больше кандидатов — за те же позиции. Типичный кандидат тратит 2–4 часа на доработку CV под вакансию — до того как узнаёт, есть ли у него вообще шанс. Большинство инструментов помогают писать быстрее. Ни один не отвечает на предшествующий вопрос: стоит ли подавать вообще?

Есть и когнитивная ловушка: кандидат читает JD эмоционально. Первое впечатление якорит восприятие — реальные барьеры игнорируются. Реальный пример: кандидат оценил себя на 10/10 → агент выдал 4/10 с конкретным объяснением. Дельта = 6 баллов из-за одного скрытого core-требования.

**Контрциклический тезис:** чем хуже рынок для кандидата — тем выше ценность инструмента, повышающего hit rate. Чем лучше рынок — тем быстрее кандидат находит работу. В любом сценарии инструмент востребован.

---

## Решение

**Career Agent** — персональный карьерный советник для Product Manager / Product Owner. Не генератор CV — инструмент принятия решений с двумя слоями ценности:

**Слой 1 — Стоит ли подавать?**  
Агент читает вакансию глубже, чем кандидат под влиянием первого эмоционального впечатления. Извлекает реальную боль работодателя, скрытые требования, архетипный сигнал (Founder Proxy vs Executor). Выдаёт честный вердикт: *apply / apply with adaptation / don't apply*. Плохой фит → говорит прямо и экономит время.

**Слой 2 — Как выиграть эту вакансию?**  
Если фит есть — строит лучший возможный питч из реального опыта кандидата. Не generic CV — целевая история: почему именно этот кандидат — ответ на конкретную боль работодателя. Включает self-review pass: CV проверяется против Adaptation Plan перед отправкой.

**Как работает:**

```
RSS auto-discovery → Telegram уведомление
→ Phase 1: Deep JD Analysis (архетип, скрытые требования, барьеры)
→ Phase 2: Fit Scoring (вердикт · Fit Breakdown ✅/⚠️/❌ · Adaptation Plan)
→ [пользователь: apply или skip]
→ Phase 3: CV Draft (archetype-aware framing)
→ Phase 3.5: Self-Review (cross-check vs Adaptation Plan)
→ CV.pdf → Telegram
→ Phase 4: Cover Letter (2 варианта: narrative + bullets)
```

Пользователь принимает только два решения: подавать или нет, и одобрить CV. Всё остальное — автоматически.

---

## Для кого

**Product Manager, Product Owner, Project Manager** в активном поиске работы. Пассивный поиск — вторичный сегмент.

Специализация на PM-ролях не случайна: fit analysis понимает PM-архетипы, оценивает PM-специфичные сигналы (владение продуктом, discovery vs delivery, масштаб команды), адаптирует фреймирование CV под то, что роль реально требует — а не под то, что написано в JD.

---

## Дифференциация

| Что | Чем отличается |
|---|---|
| **Decision-first** | Вердикт apply/skip — до начала работы с CV. Ни один аналог (Jobscan, Teal, Resume.io) не делает go/no-go до генерации документа |
| **PM archetype-aware** | Fit analysis различает Founder Proxy vs Executor — архетипный мисматч тихий убийца найма, который generic инструменты не видят |
| **Self-review loop** | Phase 3.5: агент проверяет свой же CV против Adaptation Plan. Кандидат видит уже перепроверенную версию |
| **Честный скоринг** | Говорит «не подавай» когда фит слабый. Не оптимизирует под отправку — оптимизирует под результат |

---

## Трекшн и доказательства

- **~80 вакансий обработано** одним пользователем в реальном активном job search (данные в SQLite)
- **2 пользователя** с разными профилями (skill type: `pm` и `generic`)
- **e2e верификация:** полный pipeline (analyze → CV → cover) прогнан, стоимость зафиксирована ($0.10, 2026-06-02)
- **~32 часа разработки** (из effort-log.md, нижняя граница по git)
- Продукт используется автором в текущем активном job search — данные реальные, не синтетические

---

## Метрики

| Метрика | Статус |
|---|---|
| Вакансий проанализировано / пользователь / месяц | Трекается в DB |
| Apply rate (подал vs skip после анализа) | Трекается в DB |
| CV generation rate (go-вердикт → CV) | Трекается в DB |
| Cache hit rate + cost per vacancy | Трекается в DB (`llm_usage`) |
| Time-to-placement | Планируется (self-report через бот) |

Actuals не опубликованы — следующий шаг: `docs/discovery/product-metrics.md` из реальных данных SQLite.

---

## Юнит-экономика

COGS ~$0.32 на вакансию (полный pipeline: fetch → analyze → CV → cover) на Claude Sonnet 4.6 с prompt caching.

| Модель | Revenue | COGS | Gross Margin |
|---|---|---|---|
| $0.99 / вакансия | $0.99 | $0.32 | **68%** |
| $4.99 / вакансия | $4.99 | $0.32 | **94%** |
| $19.99 / мес, 50 вак | $19.99 | $16.00 | **20%** |

**Оптимальная модель:** pay-per-vacancy ($0.99–$4.99) или подписка с лимитом ≤ 30 вак/месяц.

---

## Что построено / Что дальше

**Работает сегодня:**
- Полный 5-фазный pipeline с self-review
- Telegram UI (aiogram 3.x) + Web tracker (FastAPI + HTMX)
- Multi-user архитектура (users table, user-scoped vacancies, skill_type routing)
- Prompt caching + Extended Thinking (Claude Sonnet 4.6)
- Микросервисы: jd-parser (URL → Markdown), pdf-service (Markdown → PDF)
- RSS auto-discovery + webhook push
- 2 skill types: `pm` (archetype-aware) + `generic`
- 291 тест, e2e верифицировано

**Следующий значимый шаг:**  
Deterministic/Cognitive split — выделить детерминированную оркестрацию (файловые операции, dedup, рендеринг PDF) в Python FSM; вызывать LLM только на 3 когнитивных точках вместо ~43 смешанных шагов. Цель: снижение latency, снижение COGS, предсказуемый output.

---

## Открытые риски

- **Нет внешней валидации** — вся discovery self-reported; интервью с посторонними пользователями не проводились
- **Competitive landscape не исследован** — позиционирование строится на продуктовом тезисе, не на рыночных данных
- **Fit scoring calibration manual** — нет автоматического feedback loop «вердикт → реальный результат»
- **Onboarding не достроен** — AI Interview System (ключевой дифференциатор, глубокий профиль) в статусе 🔴 DESIGN REQUIRED

---

## Что сделано как PM

| Артефакт | Что демонстрирует |
|---|---|
| Product thesis + two-sided pain analysis | Problem discovery, рыночное мышление |
| ADR-01: Pivot from generic agent to focused vertical service | Стратегическое решение, product identity |
| EPIC-21: Deterministic vs Cognitive split | Technical PM thinking, влияние на unit economics |
| 21 Epic с blocker-ordered tasks + acceptance criteria | Requirements management, delivery execution |
| Tokenomics.md: 6 реальных прогонов, формула верифицирована | Unit economics, data-driven подход |
| PIVOT-PLAN.md: фазы с dependencies + success criteria | Структурированное планирование |
| Hypotheses framework (H-001, H-002, H-003) | Экспериментальное мышление, PM rigour |
