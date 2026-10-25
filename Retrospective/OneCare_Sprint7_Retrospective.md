# OneCare Systems: Sprint 7 Retrospective

**Sprint:** 2026/10/07 – 2026/10/25

**Date:** 2026/10/25

## Attendees

- Vipusa
- Thushajini
- Sweeskaran
- Kenuja

## Went Well

1. The application was deployed by the 25 Oct deadline.
2. All planned backend modules were merged: Centralized Audit Logging (DDP-#25), RBAC Table 9 across new modules (DDP-#23), Clinic Configuration and System Admin (DDP-#31), Public Self-Service Booking (DDP-#33), In-house Dispensing (DDP-#30), Reporting with CSV/PDF export (DDP-#32) and Chatbot Knowledge Base Management, Spring side (DDP-#34).
3. JWT Logout with Redis token invalidation (DDP-13) and three follow-up tickets (Auth/CORS, Appointment-Patient-Invoice integration, Clinical Notes alignment) were closed within days of being raised, so integration gaps did not pile up.
4. The Django chatbot service was delivered end to end: Stage 2 Knowledge Base Ingestion (DDP-36), Stage 3 Retrieval & Guardrails (DDP-#37, 7/7) and Stage 4 LLM Integration (DDP-#38, 3/3).
5. The frontend covered every role: Common Configuration & Design System (DDP-42, 7/7), Authentication Pages (DDP-#43), Doctor Module (DDP-#44, 5/5), Admin pages, Super Admin pages and the Pharmacy Dashboard.
6. 58 of 59 tracked board items closed, with 31 PRs linked to closed issues across the backend, chatbot, frontend and infra repositories.
7. Testing Creation Ticker were closed without doing because each ticket add unit testing in bakcend and fronted and cahtbot

## To Improve

1. The 18 Oct finish target was missed. Only 20 of 57 closed items were done by 18 Oct; 37 closed after it. The 18–25 Oct buffer was used for development, not for fixes and deployment.
2. Testing and quality gates lagged. The application was deployed without an enforced coverage gate, and no UAT with clinic staff is recorded on the board.
3. Workload was uneven again.
4. PRs were large and batched.
5. The tracker was still updated in batches and left incomplete
6. Because tasks were hurried, UI polish and the client's feedback items (font size, chatbot hints, medical symbols, logo placement) were not addressed.

## Action Items

| Action | Owner | Due |
| --- | --- | --- |
|  FR-traced test suite and enforced coverage gate | Thushajini | Before client handover |
| Smoke test the deployed system end to end (login, booking, consultation, prescription, dispensing, external dispensing, invoice, chatbot) and record the results | All Members | Before client handover |
| List and triage known defects and shortcuts taken to hit the deadline; fix the critical ones first | All Members | Before client handover |
| Prepare the handover pack: deployment steps, `.env.example`, Swagger link, user roles and credentials process | Sweeskaran | Before client handover |
| Draft the UI improvement backlog from client feedback and prioritise it (see Post-Handover Focus) | Thushajini, Kenuja | After client handover |

## Metrics

- Board items tracked: 59 (19 main tickets done and 2 open, 38 sub-issues done)
- Items completed: 57
- Completion rate: 97% of tracked items (main tickets: 19 of 21)
- Closed by the 18 Oct target: 20 of 57 (35%)
- Closed after 18 Oct: 37 of 57 (65%), of which 22 in the last five days
- PRs linked to closed issues: 31
- Closed items by owner: Vipusa 22, Sweeskaran 17, Kenuja 8, Thushajini 4, unassigned 5 (DDP-42 shared by Thushajini and Vipusa)
- CI pipeline pass rate:
- Test coverage:

Counts are taken from the board export and cover only the items in it; update them if other tickets belong to this sprint.

## Completed Work

| Issue ID | Task | Status |
| --- | --- | --- |
| DDP-13 | JWT Logout & Redis Token Invalidation (Kenuja, PR #51) | Completed |
| Follow-up (backend #49) | Authentication and CORS Configuration Enhancements (Vipusa, PR #50) | Completed |
| Follow-up (backend #52) | Appointment, Patient Management and Invoice Workflow Integration (Kenuja, PR #53) | Completed |
| Follow-up (backend #44) | Align Clinical Notes between Appointment and Prescription (Vipusa, PR #47) | Completed |
| DDP-#33 | Public self-service appointment booking without login (Vipusa, PR #62) | Completed |
| DDP-#25 | Centralized Audit Logging (Thushajini, PR #65) | Completed |
| DDP-#31 | Clinic configuration and system administration APIs (Vipusa, PR #66) | Completed |
| DDP-#34 | Chatbot Knowledge Base Management, Spring side (Sweeskaran, PR #67) | Completed |
| DDP-#28 | Docker Compose and environment config for new modules (Vipusa, PR #68) | Completed |
| DDP-#30 | In-house dispensing APIs (Kenuja, PR #71) | Completed |
| DDP-#32 | Clinic and pharmacy reporting APIs with CSV/PDF export (Thushajini, PR #73) | Completed |
| DDP-#23 | Apply Table 9 RBAC to all new API modules (Vipusa, PR #75) | Completed |
| DDP-36 | Chatbot Stage 2: Knowledge Base Ingestion, KB-01, KB-02, KB-04 (Sweeskaran, PR #12) | Completed |
| DDP-#37 | Chatbot Stage 3: Retrieval & Guardrails, CHAT-01 to CHAT-07 (Sweeskaran, PR #26) | Completed |
| DDP-#38 | Chatbot Stage 4: LLM Integration, CHAT-08 to CHAT-10 (Sweeskaran, PR #27) | Completed |
| DDP-42 | Common Configuration & Design System, CFG sub-issues 7/7 (Thushajini, Vipusa, PRs #29, #32, #33) | Completed |
| DDP-#43 | Authentication Pages, AUTH-01 to AUTH-03 (Vipusa, PRs #39, #40) | Completed |
| DDP-#44 | Doctor Module, DOC-01 to DOC-04 and parent (Vipusa, PRs #47, #48, #58, #98) | Completed |
| ADM-02, 03, 04, 05, 07 | Admin: Register Patient, Patient Search & Edit, Book Appointment, Appointment Hub, Billing (Kenuja, PRs #59, #60, #87) | Completed |
| SADM-01, 02, 03, 04, 05, 07 | Super Admin: Dashboard, User Management, Create/Edit User, Clinic Configuration, Access Control, System Maintenance (Vipusa, PRs #77, #100) | Completed |
| PHARM-01 | Pharmacy Dashboard (Thushajini, PR #86) | Completed |
| DDP-#49 | Frontend Docker & Deployment, DKR sub-issues 6/6 (Vipusa, infra PR #12) | Completed |

## Root Cause Analysis

The 25 Oct deadline pushed the team to prioritize feature delivery and deployment over verification. Development finished late: 65% of closed items landed after the 18 Oct target, so the plan to test and deploy in parallel with development did not happen, and the buffer week was spent finishing features. Tests and CI gates sit at the end of the dependency chain, so they were the first thing to be squeezed out.

Workload balance improved only partly. The chatbot work stayed with one person and the frontend work concentrated on another, so Sprint 6's imbalance moved from one person to another instead of being removed. Large combined PRs and bulk issue closures made review harder and hid real progress until the end.

Speed was gained by hurrying, and the cost fell on UI polish, edge cases and the client's usability feedback, which were left for after handover.

## Lessons Learned

1. Testing and CI gates need their own tickets and dates early in the plan; if they wait for all features, they are cut when time runs short.
2. Rebalancing needs numbers, not intent: check closed and open items per person midway through the sprint, not at the retrospective.
3. Small PRs, one per sub-issue, keep review fast and keep the tracker honest; batching four to seven issues in one PR repeats the Sprint 6 problem.
4. A missed internal target (18 Oct) should trigger a scope decision at the time, not silently consume the buffer.
5. Client feedback should be turned into tickets when received, so it does not depend on spare time at the end.

## Post-Handover Focus

The deadline has passed and the application is deployed. The following is planned only if time permits after handover to the client, and is not committed scope.

1. **Stabilise first.** run the smoke test, fix critical defects and complete the handover pack.
2. **UI improvements after handover.** Candidates from the client feedback report:
   - Reposition the logo within the designated red rounded region.
   - Increase font sizes for readability, especially for elderly users.
   - Add guidance hints in the chatbot for first-time users.
   - Add medical-related symbols using the samples the client provided.
3. **Hurried-work polish.** Review consistency across role dashboards, responsive behaviour from 360px, colour contrast (4.5:1) and Tamil translations.