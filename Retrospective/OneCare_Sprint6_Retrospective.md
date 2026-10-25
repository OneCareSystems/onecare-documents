# OneCare Systems: Sprint 6 Retrospective

**Sprint:** 2026/09/16 – 2026/10/06

**Date:** 2026/10/09

## Attendees
- Vipusa
- Thushajini
- Sweeskaran
- Kenuja

## Went Well
1. All four sprint 5 carry-over tasks (DDP-14 to DDP-17) were closed, finishing the security hardening that had been deferred. 
2. RBAC with `@PreAuthorize` and role redirects, Argon2id password hashing with account lockout, and SMTP-based password reset with signed tokens and forced change are now in place.
3. Six of the seven backend API modules were completed and merged (User & Access, Patient, Appointment & Queue, Medicine & Inventory, Prescription, Billing / Invoice).
4. Work was split across all four members, with each owning at least one API module through its own PR (#35 to #42).
5. Environment configuration for dev / staging / prod was delivered (PR #27), giving the team a consistent setup across environments.
6. The Django chat service scaffold (INFRA-01) was completed, so chatbot work has a base to build on.

## To Improve
1. External Dispensing APIs (DDP-24, PR #41) and Stage 1 Environment Setup (DDP-35) were sent back with changes requested and did not close in the sprint.
2. INFRA-02 was started but not finished, and INFRA-03 and INFRA-04 were ticketed but no work was started on them.
3. Workload was uneven: Sweeskaran held 8 of 16 items, including most of the open ones, while Thushajini held 1.
4. Sprint 5 tasks slipped into this sprint, which used capacity that was planned for new work.
5. Issue notes were not updated as work progressed.

## Action Items
| Action | Owner | Due |
|--------|-------|-----|
| Address review comments and get PR #41 (DDP-24, External Dispensing APIs) merged | Kenuja | Sprint 7 – Week 1 |
| Address review changes on DDP-35 (Stage 1 Environment Setup) and resubmit | Sweeskaran | Sprint 7 – Week 1 |
| Rebalance sprint 7 assignments and move some of Sweeskaran's chatbot / infra items (INFRA-02 to 04) to members with lower load | Sweeskaran, Thushajini | Sprint 7 – Week 1 |
| Break down all remaining tickets up to DDP-50, assign owners, and agree a faster pace with daily progress check-ins until 25 Oct | All Members | Sprint 7 – Week 1 |
| Prepare the test plan and deployment checklist (staging run-through, environment config, Docker Compose) so testing and deployment can start while the last tickets are finishing | Sweeskaran | Sprint 7 – Week 1 |
| Clean up the tracker: fix statuses and typos, and update the notes on DDP-14 to DDP-17 | Vipusa | Sprint 7 – Week 2 |
| Agree on a definition of done and a status vocabulary, and review open PRs within 1 working day | All Members | Next Sprint Planning |

## Metrics
- Issues planned: 16
- Issues completed: 11
- Issues partially completed: 3 (DDP-24 and DDP-35: changes requested; INFRA-02: started, not finished)
- Issues not completed: 2 (INFRA-03, INFRA-04: ticket created only)
- Sprint completion rate: 55%
- CI pipeline pass rate:
- Test coverage:

## Completed Work
| Issue ID | Task | Status |
|----------|------|--------|
| DDP-14 | RBAC: permissions, `@PreAuthorize`, role redirects (Vipusa, PR #23) | Completed |
| DDP-15 | Password security: Argon2id and account lockout (Vipusa, PR #26) | Completed |
| DDP-16 | Password reset: SMTP, signed token, forced change (Vipusa, PR #28) | Completed |
| DDP-17 | Environment configuration: dev / staging / prod (Sweeskaran, PR #27) | Completed |
| DDP-18 | User & Access Management APIs (Sweeskaran, PR #35) | Completed |
| DDP-19 | Patient Management APIs (Sweeskaran, PR #36) | Completed |
| DDP-20 | Appointment & Queue APIs (Kenuja, PR #37) | Completed |
| DDP-21 | Medicine & Inventory APIs (Thushajini, PR #38) | Completed |
| DDP-22 | Prescription & Prescription Item APIs (Vipusa, PR #39) | Completed |
| DDP-23 | Billing / Invoice APIs (Vipusa, PR #42) | Completed |
| INFRA-01 | Scaffold Django Chat Service (Sweeskaran) | Completed |
| DDP-24 | External Dispensing APIs (Kenuja, PR #41) | Partially Completed |
| DDP-35 | Stage 1: Environment Setup, Chatbot (Sweeskaran) | Partially Completed |
| INFRA-02 | Install & Pin Dependencies, Chatbot (Sweeskaran) | Partially Completed |

## Unfinished Work and Reasons
| Issue ID | Task | Reason |
|----------|------|--------|
| DDP-24 | External Dispensing APIs | PR #41 was reviewed and changes were requested; the changes were not completed before sprint end. |
| DDP-35 | Stage 1: Environment Setup (Chatbot) | Reviewed and changes requested; not yet resubmitted. |
| INFRA-02 | Install & Pin Dependencies (Chatbot) | Work was started but could not be finished before the sprint ended. |
| INFRA-03 | Local MySQL / Metadata Table Setup (Chatbot) | Ticket created only; work not started. |
| INFRA-04 | Docker Compose (Infra) | Ticket created only; work not started. |

## Root Cause Analysis
The sprint had two main pressures. First, the four sprint 5 carry-over tasks (RBAC, password security, password reset and environment configuration) were added on top of the seven new API modules, so planned scope was larger than a normal sprint. Second, work was concentrated on Sweeskaran, who owned the environment configuration, two API modules and all of the chatbot / infra items. The backend API modules were prioritised and finished, while the chatbot and infra items behind them were sent back in review, only partly started, or not started.

The two review rejections (DDP-24 and DDP-35) suggest that work was submitted before it met the review expectations, or that expectations were not agreed up front. Tracker notes and statuses were also not kept current, which hid how much of the sprint 5 backlog had actually been resolved.

## Lessons Learned
1. Carry-over work should be planned explicitly in the next sprint and counted against capacity, rather than added on top of a full sprint.
2. Spreading ownership more evenly, especially for cross-cutting work like infra and chatbot, reduces the risk of one person becoming a bottleneck.
3. Agreeing on review expectations and a shared definition of done before opening a PR should reduce changes-requested cycles.
4. With a fixed deadline, testing and deployment need their own time in the plan and should run alongside development, not wait until the end.
5. Review turnaround and rework are now the biggest threats to the date, so PRs should be small, reviewed quickly and fixed the same day.

## Sprint 7 Focus

The project deadline of 25 October was announced after this sprint was planned. Every ticket up to DDP-50 must now be finished, tested and deployed within about two weeks of this retrospective. At the pace of sprint 6, which completed 11 of 16 items, the remaining backlog will not fit, so the team has to work faster than planned and start testing and deployment before all development is finished, not after.

1. Finish every planned ticket up to DDP-50 by 18 October, to avoid last-minute errors, starting with closing DDP-24 and DDP-35 by resolving review feedback.
2. Complete the chatbot and infra foundation quickly (INFRA-02 dependencies, INFRA-03 MySQL / metadata tables, INFRA-04 Docker Compose), since testing and deployment depend on it.
3. Run testing and deployment in parallel with development, using the period from 18 to 25 October as a buffer for fixes and the final deployment.
4. Rebalance ownership across all four members and keep the tracker accurate (statuses, notes and story points) so progress against the deadline is visible.
