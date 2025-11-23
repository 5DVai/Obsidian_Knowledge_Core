product lifecycle 


Here’s the standard end-to-end lifecycle. Use it as a gated playbook.

Idea → Commercial Use → Sale
Phase 0 — Strategy & Discovery (SRR)

Objective: Prove there is a valuable problem and a real market.
Key work: Market sizing, competitor teardown, user interviews, JTBD, feasibility spike, high-level cost model.
Artifacts: Problem statement, opportunity canvas, persona set, feasibility notes.
Gate: System Requirements Review (SRR).
Exit: Problem, users, constraints, and success metrics are defined.

Phase 1 — Product Definition (PRD)

Objective: Lock scope and outcomes.
Key work: PRD, Technical PRD, top workflows, acceptance criteria, non-goals.
Artifacts: PRD v1.0, TPRD v1.0, KPI tree, risk log.
Gate: PRD Sign-off.
Exit: Clear scope, KPIs, and risks accepted.

Phase 2 — Architecture & Design (PDR → CDR)

Objective: Choose architecture and UX that meet KPIs.
Key work: Control/data plane, data model, OpenAPI 3.1 draft, threat model (STRIDE), DPIA, UX flows, design system.
Artifacts: Architecture decision records, OpenAPI draft, Figma prototypes, test strategy, SLOs, observability plan.
Gates: Preliminary Design Review (PDR) → Critical Design Review (CDR).
Exit: Interfaces, SLOs, and UX validated with users.

Phase 3 — Planning & Governance

Objective: Make delivery predictable.
Key work: Roadmap, budget, RACI, staffing, sprint hygiene, change control, feature flags.
Artifacts: Program plan, resourcing, risk burndown, security/privacy plan.
Gate: Execution Go.
Exit: Team, plan, and controls in place.

Phase 4 — Walking Skeleton (TRR-Alpha)

Objective: Prove the vertical slice works end-to-end.
Key work: WS voice ↔ STT ↔ plan/act ↔ tool call ↔ TTS ↔ client playback; budget gate; signed audit.
Artifacts: Running slice, latency and cost baselines, runbooks.
Gate: Technical Readiness Review (TRR).
Exit: P95 latency and cost within targets; core risks retired.

Phase 5 — Alpha (closed)

Objective: Validate usefulness with real users.
Key work: 100–300 users, logs + traces, rapid iteration, failure analysis.
Artifacts: Alpha KPI report, incident register, consent records.
Gate: Alpha Exit.
Exit: ≥70–80% task completion on top flows; clear fix list.

Phase 6 — Beta (open)

Objective: Validate scale, reliability, and monetization.
Key work: Mobile, payments, pricing experiments, support workflows, multi-region readiness, data retention enforcement.
Artifacts: Billing integration, GTM plan, support playbooks, DPA/ToS/Privacy Policy.
Gate: Launch Readiness Review (LRR) + Operational Readiness Review (ORR).
Exit: SLOs met at target load, churn within target, support SLAs green.

Phase 7 — GA Launch

Objective: Release to the public.
Key work: Staged rollout, incident response on-call, comms and content, feedback loop.
Artifacts: Release notes, status page, RCA template, deprecation policy.
Gate: GA Go/No-Go.
Exit: GA live; monitoring and rollback proven.

Phase 8 — Commercial Operations (B2C first)

Objective: Sustainable growth and unit economics.
Key work: Pricing tiers, free-to-paid conversion, fraud controls, tax/VAT, refunds, app-store compliance, CRM, lifecycle emails.
Artifacts: Billing dashboards, LTV/CAC model, fraud rules, tax nexus mapping.
Exit: Positive retention cohorts, gross margin targets met.

Phase 9 — SMB/Enterprise Readiness (optional scale-up)

Objective: Sell into orgs without re-architecture.
Key work: SSO/SAML, SCIM, enterprise RBAC, audit export, DLP hooks, procurement pack (SOC 2, pentest, DPIA), SLAs.
Artifacts: Security questionnaire answers, CAIQ, pen test, SOC 2 report, enterprise pricing.
Gate: Enterprise Ready.
Exit: Passed pilot security reviews; first paid logos.

Phase 10 — Continuous Improvement

Objective: Improve product and costs.
Key work: A/B tests, backlog grooming, release cadence, cost optimization, model routing, usage analytics, deprecations.
Artifacts: Quarterly product review, architecture review board notes, cost reports.
Exit: KPIs trending to plan; error budgets respected.

Commercialization Paths
A) Sale to Consumer Buyer (checkout/app stores)

Funnels: Land → activate → “first success” → subscribe.

Payments: Stripe/Apple/Google; retries, dunning, prorations, refunds.

Compliance: PCI-DSS SAQ-A scope, GDPR, CCPA, COPPA exclusion, regional taxes (VAT/GST).

Support: SLA tiers, help center, in-app diagnostics, abuse and content policies.

Metrics: Activation rate, WAU/MAU, conversion, churn, ARPU, LTV/CAC, payback.

B) Sale to Business Buyer (contracts)

Readiness: SSO/SAML, SCIM, audit export, data retention controls, admin console.

Security Pack: SOC 2 Type II, pen test, VDP, DPAs, sub-processor list, RoPA.

Procurement: MSA, DPA, SLA, SOW, pricing schedule, insurance.

Sales Ops: CRM stages, MEDDICC/Challenger hygiene, reference accounts.

Metrics: Win rate, sales cycle, ACV, gross margin, NRR.

C) Company Sale / Acquisition (exit)

Prep: Clean IP chain, contributor CLAs, license scan, privacy compliance, data room.

Data Room: PRD/TPRD, code + tests, infra diagrams, metrics, RCAs, SOC2, contracts, cap table.

Process: Teasers → NDAs → mgmt meetings → LOI → diligence → SPA → close.

Risks: Data liabilities, vendor lock-in, abnormal churn, undocumented IP.

Mitigation: Clear DPAs, portability plan, escrowed build, key-person coverage.

Phase Gates and Exit Criteria (summary)
Gate	Focus	Pass criteria
SRR	Problem/market	Personas set, success metrics, feasibility ✓
PRD	Scope	PRD/TPRD approved, risks logged ✓
PDR	Architecture	Interfaces, data model, threat model draft ✓
CDR	Design freeze	OpenAPI 3.1, UX flows, SLOs, test plan ✓
TRR	Tech ready	Walking skeleton hits latency/cost, runbooks ✓
Alpha Exit	Usefulness	≥75% task completion on top flows ✓
LRR/ORR	Launchable	SLOs at scale, support+security+GTM ready ✓
GA	Public launch	Rollout staged, rollback tested ✓
Enterprise Ready	Org sales	SSO/SCIM, security pack, pilot passed ✓
RACI (condensed)

Product: PRD, roadmap, KPIs.

Platform Eng: architecture, infra, SLOs, observability.

Voice Eng: STT/TTS pipeline, latency.

Agentic Eng: planner, tools, memory.

Security/Privacy: threat model, DPIA, audits, compliance.

Design/Research: UX flows, design system, tests.

GTM: pricing, positioning, channels, content.

Support/SRE: on-call, incident mgmt, runbooks.

Core Metrics by Stage

Discovery: Qualified problem count, interview signal strength.

Definition: Requirement volatility, risk coverage.

Build: P50/P95 RTT, error rate, cost/min, test coverage.

Alpha/Beta: Task completion, time-to-value, retention W1/W4, NPS.

GA: Conversion, churn, ARPU, margin, incident rate.

Scale: NRR, enterprise win rate, security findings burn-down.

This is the industry-standard path. Start at SRR, then drive each gate to closure with artifacts and exit criteria.