# Thesis Proposal

> Кваліфікаційний проєкт бакалавра · Спеціальність F2 «Інженерія програмного забезпечення»

| Поле | Значення |
|---|---|
| Здобувач | Сможевських К. |
| Науковий керівник | [ПІБ наукового керівника] |
| Статус | Попередня пропозиція теми |

## 1. Попередня робоча назва

**Українською:** Мікросервісна система краудінвестингу та форвардних контрактів для малого бізнесу з біржовим рушієм зведення заявок

**English:** A Microservice-Based Crowdinvesting and Forward Contract Platform for Small Businesses with an Order Matching Engine

## 2. Актуальність та проблематика

Малий бізнес, зокрема агровиробники, має обмежений доступ до банківського кредитування через вимоги до застави та тривалі процедури погодження. Краудінвестинг дає змогу залучати фінансування від приватних інвесторів невеликими сумами, однак поширені моделі мають три системні обмеження:

1. **Низька ліквідність інвестицій.** Інвестор, як правило, не може вийти з інвестиції до завершення проєкту.
2. **Відсутність контролю цільового використання коштів.** Кошти передаються одноразово, без прив'язки до виконання етапів робіт.
3. **Неповна модель ризиків.** Оцінювання бізнесу не враховує специфічних факторів воєнного стану: замінування територій і близькості до зони бойових дій.

Інженерна складність задачі полягає в тому, що система керує грошовими коштами в розподіленому середовищі. Це вимагає гарантій узгодженості даних між сервісами, запобігання перепідписці кампаній за конкурентних запитів, атомарного розрахунку за угодами, коректного округлення під час розподілу виплат, ідемпотентної обробки повідомлень і відновлення після часткових збоїв. Вторинний ринок додатково вимагає рушія зведення заявок із детермінованою поведінкою та низькою затримкою.

## 3. Ідея продукту

**Тип системи:** хмароорієнтована подієво-керована мікросервісна платформа з двома контурами:

- **первинний ринок** — залучення фінансування через форвардні контракти на майбутню продукцію з ескроу-механізмом поетапного вивільнення коштів;
- **вторинний ринок** — обіг інвестиційних часток між інвесторами через біржовий рушій зведення заявок.

Основний обсяг роботи зосереджено в серверній частині. Веб-інтерфейс має мінімальний функціонал і призначений для демонстрації сценаріїв використання.

Сервісний склад:

| Сервіс | Відповідальність |
|---|---|
| API Gateway | Єдина точка входу, маршрутизація, автентифікація, обмеження частоти запитів |
| Campaign Service | Інвестиційні кампанії, форвардні контракти, етапи робіт |
| Scoring Service | Рейтинг бізнесу, моделювання ризиків і дохідності |
| Portfolio Service | Профіль ризику інвестора, формування портфеля |
| Ledger & Escrow Service | Рахунки, облік за принципом подвійного запису, ескроу, транші |
| Matching Engine Service | Книга заявок, зведення угод, ринкові дані |
| Distribution Service | Фіксація реєстру власників, розподіл прибутку |
| Notification Service | Сповіщення користувачів про події системи |

## 4. Відмінності від існуючих рішень

| Критерій | Типові краудінвестингові платформи | Запропонована система |
|---|---|---|
| Інструмент інвестування | Частка в капіталі компанії або позика | Форвардний контракт на майбутню продукцію або частку від продажів |
| Вихід з інвестиції до завершення проєкту | Як правило, обмежений або неможливий | Вторинний ринок часток із безперервною книгою заявок |
| Контроль використання коштів | Одноразова передача коштів | Ескроу з вивільненням траншів за підтвердженими етапами робіт |
| Оцінювання ризиків | Загальні фінансові показники | Бальна модель з урахуванням воєнних ризиків і моделювання методом Монте-Карло |
| Формування портфеля | Переважно ручний вибір проєктів | Автоматичний розподіл відповідно до профілю ризику інвестора |

В Україні діє інструмент аграрних розписок, який дає змогу агровиробникам отримувати фінансування під майбутній урожай. Проте він є двостороннім зобов'язанням між виробником і кредитором і не передбачає майданчика для залучення коштів від роздрібних інвесторів.

## 5. Ключовий функціонал (Core Features)

**F1. Управління кампаніями та форвардними контрактами.** Бізнес створює кампанію із сумою фінансування, етапами робіт і умовами контракту: продаж частини майбутньої продукції за зниженою ціною або відрахування відсотка від продажів. Життєвий цикл кампанії реалізовано як скінченний автомат: збір коштів → ескроу → виконання етапів → продаж продукції → розподіл прибутку → завершення. Типи контрактів розширюються через спільний інтерфейс без зміни ядра системи.

**F2. Скоринг бізнесу та моделювання ризиків.** Бальна модель формує кредитний рейтинг на основі історії бізнесу, його розміру, галузі, регіональних погодних ризиків і воєнних ризиків (замінування територій, близькість до зони бойових дій). Прогнозована дохідність розраховується методом Монте-Карло як розподіл можливих результатів з оцінкою ймовірності збитку та Value at Risk.

**F3. Профілювання інвестора та формування портфеля.** За результатами опитувальника система визначає профіль ризику інвестора (консервативний, помірний, агресивний) і розподіляє суму між кампаніями різних галузей та рівнів ризику відповідно до профілю.

**F4. Ескроу та облік коштів.** Кошти інвесторів резервуються на віртуальному ескроу-рахунку і вивільняються бізнесу траншами після підтвердження етапів робіт верифікатором. Усі рухи коштів фіксуються за принципом подвійного запису. Узгодженість операцій між сервісами забезпечують патерни Saga та Transactional Outbox. Перепідписку кампанії за конкурентних запитів виключено механізмом атомарного резервування.

**F5. Біржовий рушій зведення заявок.** Рушій веде книгу заявок Bid/Ask для кожного інструмента в оперативній пам'яті та зводить угоди за алгоритмом Price-Time Priority. Підтримуються лімітні та ринкові заявки, часткове виконання і скасування. Кожна книга заявок обробляється в одному потоці, що забезпечує детермінований порядок виконання без блокувань.

**F6. Розрахунок за угодами.** Розрахунок виконується за принципом «поставка проти оплати»: кошти й частка переходять між учасниками атомарно. Кошти під заявку на купівлю резервуються в момент її подання, що виключає повторне використання тих самих коштів. Кожна угода зберігається як незмінний запис.

**F7. Розподіл прибутку.** Після продажу продукції система фіксує реєстр власників часток на дату розподілу і пропорційно нараховує виплати. Залишок від округлення розподіляється методом найбільшого залишку, тому сума виплат завжди дорівнює сумі до розподілу.

## 6. Нефункціональні вимоги та критерії перевірки

| Вимога | Спосіб перевірки |
|---|---|
| Узгодженість коштів: сума всіх проводок у реєстрі дорівнює нулю | Інтеграційні тести, перевірка інваріантів після навантажувальних тестів |
| Відсутність перепідписки кампаній за конкурентних запитів | Навантажувальні сценарії k6 |
| Ідемпотентність: повторна доставка повідомлення не змінює стан системи | Інтеграційні тести з Testcontainers |
| Відмовостійкість: коректне відновлення після збою окремого сервісу | Тести з примусовою зупинкою сервісів, компенсуючі транзакції |
| Продуктивність рушія: пропускна здатність і затримка (p50, p99) | Мікробенчмарки JMH |
| Продуктивність API під навантаженням | Сценарії k6: load, stress, spike |
| Безпека та якість коду | Quality gate SonarQube, сканування Trivy та OWASP Dependency-Check |

## 7. Технологічний стек

| Категорія | Технології |
|---|---|
| Мови програмування | Java 21, TypeScript |
| Фреймворки | Spring Boot 3, Spring Cloud Gateway, Spring Security, Spring Data JPA, Spring for Apache Kafka, Resilience4j, React |
| Архітектурні патерни | Мікросервісна архітектура, Event-Driven Architecture, Saga (оркестрація), Transactional Outbox, Idempotent Consumer, CQRS, Database per Service, Circuit Breaker |
| Бази даних і сховища | PostgreSQL, Redis |
| Брокер повідомлень | Apache Kafka |
| Безпека | Keycloak (OAuth 2.0, OpenID Connect), JWT, RBAC |
| Тестування | JUnit 5, Testcontainers, JMH, k6 |
| DevOps / DevSecOps | Docker, Docker Compose, Kubernetes, Helm, GitHub Actions, SonarQube, Trivy, OWASP Dependency-Check, Gitleaks |
| Спостережуваність | OpenTelemetry, Prometheus, Grafana, Jaeger, Loki |
| Документування | C4 Model (Structurizr), ADR, OpenAPI 3, Markdown (Docs-as-Code) |

**Обґрунтування ключових рішень:**

- **Java 21 та Spring Boot** — зріла екосистема для фінансових систем із підтримкою транзакцій, безпеки та інтеграції з Kafka; віртуальні потоки спрощують обробку великої кількості одночасних запитів.
- **PostgreSQL** — ACID-транзакції для реєстру коштів, де неприпустимі втрата чи дублювання проводок.
- **Apache Kafka** — довговічний журнал подій з можливістю повторного відтворення, основа асинхронної взаємодії сервісів.
- **Redis** — атомарні операції для швидкого резервування залишку кампанії під час пікового навантаження.
- **Kubernetes** — горизонтальне масштабування сервісів і розгортання в хмарному середовищі.

## 8. Інженерна документація

Архітектуру описано моделлю C4 (рівні Context, Container, Component), ключові рішення фіксуються у форматі ADR, контракти API — у специфікації OpenAPI 3. Документація зберігається в репозиторії поруч із кодом за принципом Docs-as-Code.

## 9. Обмеження

- Система є програмним прототипом і не призначена для промислової експлуатації.
- Регуляторні вимоги до обігу фінансових інструментів не входять до предмета роботи.
- Платіжні операції емулюються; інтеграція з реальними платіжними системами не передбачена.
- Для скорингу та моделювання використовуються відкриті й синтетичні дані.

---

# English Version

## 1. Working Title

**Ukrainian:** Мікросервісна система краудінвестингу та форвардних контрактів для малого бізнесу з біржовим рушієм зведення заявок

**English:** A Microservice-Based Crowdinvesting and Forward Contract Platform for Small Businesses with an Order Matching Engine

## 2. Relevance and Problem Statement

Small businesses, including agricultural producers, have limited access to bank lending due to collateral requirements and lengthy approval procedures. Crowdinvesting enables them to raise small amounts from private investors, but common models have three systemic limitations:

1. **Low liquidity.** An investor generally cannot exit an investment before the project is completed.
2. **No control over the use of funds.** Funds are transferred in a single payment, without being tied to completed work stages.
3. **Incomplete risk model.** Business assessment ignores martial-law-specific factors such as mined territories and proximity to combat zones.

The engineering complexity stems from managing money in a distributed environment. The system must guarantee data consistency across services, prevent campaign oversubscription under concurrent requests, settle trades atomically, round payouts correctly, process messages idempotently and recover from partial failures. The secondary market additionally requires an order matching engine with deterministic behaviour and low latency.

## 3. Product Concept

**System type:** a cloud-native, event-driven microservice platform with two parts:

- **primary market** — raising funds through forward contracts on future output, with an escrow mechanism that releases funds in stages;
- **secondary market** — trading investment shares between investors through an order matching engine.

The main scope of work is the backend. The web interface is minimal and serves to demonstrate usage scenarios.

Services:

| Service | Responsibility |
|---|---|
| API Gateway | Single entry point, routing, authentication, rate limiting |
| Campaign Service | Investment campaigns, forward contracts, work stages |
| Scoring Service | Business rating, risk and return modelling |
| Portfolio Service | Investor risk profile, portfolio construction |
| Ledger & Escrow Service | Accounts, double-entry bookkeeping, escrow, tranches |
| Matching Engine Service | Order book, trade matching, market data |
| Distribution Service | Shareholder record snapshot, profit distribution |
| Notification Service | User notifications about system events |

## 4. Differences from Existing Solutions

| Criterion | Typical crowdinvesting platforms | Proposed system |
|---|---|---|
| Investment instrument | Equity stake or loan | Forward contract on future output or revenue share |
| Exit before project completion | Usually limited or impossible | Secondary market with a continuous order book |
| Control over the use of funds | Single transfer of funds | Escrow releasing tranches for verified work stages |
| Risk assessment | General financial indicators | Scoring model with war-related risks and Monte Carlo simulation |
| Portfolio construction | Mostly manual project selection | Automatic allocation based on the investor's risk profile |

Ukraine has an instrument called agrarian receipts that allows agricultural producers to obtain financing against a future harvest. However, it is a bilateral obligation between a producer and a creditor and provides no marketplace for raising funds from retail investors.

## 5. Core Features

**F1. Campaign and forward contract management.** A business creates a campaign with a funding amount, work stages and contract terms: selling part of its future output at a discount or paying a percentage of sales. The campaign lifecycle is implemented as a finite state machine: fundraising → escrow → stage execution → sale of output → profit distribution → completion. Contract types are extended through a common interface without changing the system core.

**F2. Business scoring and risk modelling.** A points-based model produces a credit rating from the business's history, size, industry, regional weather risks and war-related risks (mined territories, proximity to combat zones). Expected return is calculated with Monte Carlo simulation as a distribution of possible outcomes, including the probability of loss and Value at Risk.

**F3. Investor profiling and portfolio construction.** Based on a questionnaire, the system determines the investor's risk profile (conservative, moderate, aggressive) and allocates the amount across campaigns of different industries and risk levels according to that profile.

**F4. Escrow and fund accounting.** Investor funds are held in a virtual escrow account and released to the business in tranches after a verifier confirms each work stage. All fund movements are recorded using double-entry bookkeeping. Cross-service consistency is ensured by the Saga and Transactional Outbox patterns. Campaign oversubscription under concurrent requests is prevented by atomic reservation.

**F5. Order matching engine.** The engine maintains an in-memory Bid/Ask order book for each instrument and matches trades using the Price-Time Priority algorithm. It supports limit and market orders, partial fills and cancellations. Each order book is processed by a single thread, which guarantees a deterministic execution order without locks.

**F6. Trade settlement.** Settlement follows the delivery-versus-payment principle: funds and shares change hands atomically. Funds for a buy order are reserved when the order is placed, which prevents the same funds from being spent twice. Every trade is stored as an immutable record.

**F7. Profit distribution.** After the output is sold, the system takes a snapshot of shareholders on the distribution date and calculates proportional payouts. Rounding remainders are allocated using the largest remainder method, so the total paid out always equals the amount being distributed.

## 6. Non-Functional Requirements and Verification

| Requirement | Verification method |
|---|---|
| Fund consistency: the sum of all ledger entries equals zero | Integration tests, invariant checks after load tests |
| No campaign oversubscription under concurrent requests | k6 load scenarios |
| Idempotency: redelivery of a message does not change system state | Integration tests with Testcontainers |
| Fault tolerance: correct recovery after a single service failure | Tests with forced service shutdown, compensating transactions |
| Matching engine performance: throughput and latency (p50, p99) | JMH microbenchmarks |
| API performance under load | k6 scenarios: load, stress, spike |
| Security and code quality | SonarQube quality gate, Trivy and OWASP Dependency-Check scans |

## 7. Technology Stack

| Category | Technologies |
|---|---|
| Programming languages | Java 21, TypeScript |
| Frameworks | Spring Boot 3, Spring Cloud Gateway, Spring Security, Spring Data JPA, Spring for Apache Kafka, Resilience4j, React |
| Architectural patterns | Microservice architecture, Event-Driven Architecture, Saga (orchestration), Transactional Outbox, Idempotent Consumer, CQRS, Database per Service, Circuit Breaker |
| Databases and storage | PostgreSQL, Redis |
| Message broker | Apache Kafka |
| Security | Keycloak (OAuth 2.0, OpenID Connect), JWT, RBAC |
| Testing | JUnit 5, Testcontainers, JMH, k6 |
| DevOps / DevSecOps | Docker, Docker Compose, Kubernetes, Helm, GitHub Actions, SonarQube, Trivy, OWASP Dependency-Check, Gitleaks |
| Observability | OpenTelemetry, Prometheus, Grafana, Jaeger, Loki |
| Documentation | C4 Model (Structurizr), ADR, OpenAPI 3, Markdown (Docs-as-Code) |

**Rationale for key decisions:**

- **Java 21 and Spring Boot** — a mature ecosystem for financial systems with support for transactions, security and Kafka integration; virtual threads simplify handling a large number of concurrent requests.
- **PostgreSQL** — ACID transactions for the ledger, where lost or duplicated entries are unacceptable.
- **Apache Kafka** — a durable, replayable event log that underpins asynchronous service communication.
- **Redis** — atomic operations for fast reservation of the remaining campaign amount during peak load.
- **Kubernetes** — horizontal scaling of services and deployment in a cloud environment.

## 8. Engineering Documentation

The architecture is described with the C4 model (Context, Container and Component levels), key decisions are recorded as ADRs, and API contracts are defined in OpenAPI 3. Documentation is stored in the repository alongside the code following the Docs-as-Code approach.

## 9. Limitations

- The system is a software prototype and is not intended for production use.
- Regulatory requirements for trading financial instruments are outside the scope of this work.
- Payment operations are emulated; integration with real payment systems is not planned.
- Scoring and simulation use open and synthetic data.