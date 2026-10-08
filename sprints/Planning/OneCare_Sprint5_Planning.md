# OneCare Systems
## Sprint 5 Planning Document

## Sprint Metadata

| Field | Value |
|---|---|
| Sprint number | Sprint 5 (Weeks 1–4) |
| Focus theme | Foundation & Infrastructure |
| Sprint dates | Start: 27/05/2026 — End: 17/06/2026 |
| Total tickets | 16 |
| Total estimated hours | 81h |

---

## One-Time Setup Checklist (Sprint 5 only)

- GitHub Project Phase field set to Development on all new issues
- Repositories created (backend, frontend, infra, tests, documents)
- `develop` branch created and set as default
- `.gitignore` and `.env.example` committed
- Linter/style-guide config committed to repo root
- GitHub Actions CI workflow created and passing on first empty push — tracked as DDP-#05
- Development epics and labels created (`epic:testing`, `epic:ci-cd`, `epic:dev-setup`, `epic:data-layer`, `epic:api`, `epic:ui-frontend`, `epic:auth`, `epic:security`, `epic:documentation`)
- Coding standards document committed to `documents/standards/`
- Test framework installed and first test file committed — tracked as DDP-#01

---

## Sprint Backlog

### Thushajini

| Ticket | Title | Priority | Complexity | Est. |
|---|---|---|---|---|
| DDP-#01 | build(test-setup) — JUnit 5 + Mockito (backend), Jest + React Testing Library (frontend) | P1-Critical | Low | 4h |
| DDP-#02 | ci(coverage) — JaCoCo + Jest coverage reporter wired into CI | P1-Critical | Medium | 5h |
| DDP-#03 | docs(test-plan) — Test plan: strategy, test types, coverage targets, naming conventions | P2-High | Low | 4h |
| DDP-#04 | test(smoke) — 3 E2E smoke tests: unit (AAA), integration stub (Spring context), frontend render (RTL) | P2-High | Low | 4h |

### Vipusa

| Ticket | Title | Priority | Complexity | Est. |
|---|---|---|---|---|
| DDP-#05 | ci(pipeline) — GitHub Actions: lint, build, unit test, security scan, deploy-to-staging | P1-Critical | High | 8h |
| DDP-#06 | build(docker) — Docker Compose: Spring Boot, React, MySQL 8, Redis, health checks | P1-Critical | Medium | 6h |
| DDP-#07 | feat(backend-scaffold) — Spring Boot package structure, Swagger/OpenAPI, exception handler, CORS | P1-Critical | Medium | 6h |
| DDP-#08 | feat(frontend-scaffold) — React Router v6, Axios + JWT interceptors, role-based dashboard shell | P1-Critical | Medium | 6h |

### Sweeskaran

| Ticket | Title | Priority | Complexity | Est. |
|---|---|---|---|---|
| DDP-#09 | chore(schema) — MySQL schema from SRS Ch10 into `schema.sql` migration | P1-Critical | Low | 3h |
| DDP-#10 | feat(jpa-entities) — JPA entities: Patient, User, Appointment (fields + `@Column` only) | P2-High | Low | 4h |
| DDP-#11 | chore(seed-data) — `data.sql` seed: 4 test users, 5 sample patients, 10 sample medicines | P2-High | Low | 3h |
| DDP-#12 | chore(config) — `application.properties` for dev/staging/prod via env vars | P2-High | Low | 2h |

### Kenuja

| Ticket | Title | Priority | Complexity | Est. |
|---|---|---|---|---|
| DDP-#13 | feat(auth-login) — Login API: credential validation, JWT (30-min expiry), Redis session invalidation | P1-Critical | High | 8h |
| DDP-#14 | feat(rbac) — RBAC: `@PreAuthorize` guards, role-permission matrix, role-specific dashboard redirect | P1-Critical | High | 7h |
| DDP-#15 | security(password) — Argon2id hashing, account lockout after 5 failed attempts (15 min) | P1-Critical | Medium | 5h |
| DDP-#16 | feat(auth-reset) — Password reset: SMTP email, time-limited signed token, forced change on next login | P2-High | Medium | 6h |

---

## Sprint Milestone Verification

- GitHub Milestone "Sprint 5" created, Weeks 1–4 dates set
- All 16 issues assigned to the Sprint 5 milestone
- Each issue has: assignee, estimate, priority label, epic label, Given/When/Then AC, SRS/SDS reference

---

## Capacity Planning

| Team Member | Tickets | Estimated Hours | Role |
|---|---|---|---|
| Thushajini | 4 | 17h | Testing Lead |
| Vipusa | 4 | 26h | DevOps & Backend Setup |
| Sweeskaran | 4 | 12h | DB Config & Entities |
| Kenuja | 4 | 26h | Authentication System |
| **Total** | **16** | **81h** | |

---

## Sprint Goal

Stand up the project's foundation: testing infrastructure and CI coverage reporting, a working CI/CD pipeline with staging deploy, a Dockerized local dev environment, Spring Boot and React scaffolds, the MySQL schema and JPA entity layer, and core authentication (login, RBAC, password security, reset) — so Sprint 6 can build features on solid ground.

---

## Risks / Dependencies Going In

- **Docker Compose (DDP-#06) is a single point of failure** in the dependency graph — DDP-#07, #09, #12, and (transitively) #08, #10, #11, #13, #14, #15, #16 all sit downstream of it, directly or indirectly. If it slips, most of the sprint does too. Recommend Vipusa starts DDP-#06 on Day 1, not after DDP-#05.
- **Kenuja's auth chain** (DDP-#13 → #14/#15 → #16) is sequential and high-complexity (16h of the 26h total is rated High) — little slack if DDP-#06/#09/#10 run late.
- **Thushajini's test tickets** (DDP-#02, #04) both depend on earlier tickets in the same sprint, leaving a limited buffer if DDP-#01 slips.