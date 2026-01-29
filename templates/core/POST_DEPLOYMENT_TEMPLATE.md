# [PROJECT NAME]: Post-Deployment Operations

<!-- GENERATION: This is Step 10 of 13. Requires outputs from NORTHSTAR and ARCHITECTURE_BLUEPRINT. See GENERATION_MANIFEST.md -->

> **Tier**: 2 — Implementation (see [INDEX.md](INDEX.md))
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]
> **Generation Step**: 10 of 13 — Requires `northstar.metric`, `arch.latency_budget`, `security.audit_requirements`

Operations, monitoring, and continuous improvement for your deployed agentic application.

---

## Document Purpose

This document defines **how you operate, monitor, and improve** your agentic application after deployment. It answers:

1. How do we know the system is healthy?
2. How do we collect and act on feedback?
3. When and how do we update models?
4. How do we respond to incidents?
5. How do we control costs?
6. When do we revisit strategy?

**Relationship to Other Documents:**
- **NORTHSTAR.md** defines what success looks like
- **ARCHITECTURE_BLUEPRINT.md** defines how the system works
- **This document** defines how you keep it working and improving

---

## 1. Monitoring Dashboard

### 1.1 North Star Metric (Weekly Review)

```
┌─────────────────────────────────────────────────────────────────────┐
│  NORTH STAR: [Your Metric Name]                                     │
│                                                                     │
│  Current: ████████████░░░░░░░░ [X]%    Target: [Y]%    Trend: ↑↓   │
│                                                                     │
│  Week-over-Week: [+/-X%]                                            │
│  30-Day Trend: [Direction]                                          │
└─────────────────────────────────────────────────────────────────────┘
```

**Review Cadence**: Weekly, same day/time

**Review Questions**:
- Is the metric moving in the right direction?
- What changed this week that might explain movement?
- Are we on track for quarterly targets?

### 1.2 Input Metrics (Daily Review)

| Metric | Current | Target | Status | Owner |
|--------|---------|--------|--------|-------|
| [Input Metric 1] | [X] | [Y] | On Track / At Risk | [Team] |
| [Input Metric 2] | [X] | [Y] | On Track / At Risk | [Team] |
| [Input Metric 3] | [X] | [Y] | On Track / At Risk | [Team] |

**Alert Thresholds**:

| Metric | Warning | Critical | Action |
|--------|---------|----------|--------|
| [Metric 1] | [Threshold] | [Threshold] | [Response] |
| [Metric 2] | [Threshold] | [Threshold] | [Response] |

### 1.3 System Health Metrics

| Metric | Current | SLO | Status |
|--------|---------|-----|--------|
| **Latency P50** | [X]ms | <[Y]ms | Healthy / Degraded |
| **Latency P95** | [X]ms | <[Y]ms | Healthy / Degraded |
| **Latency P99** | [X]ms | <[Y]ms | Healthy / Degraded |
| **Error Rate** | [X]% | <[Y]% | Healthy / Degraded |
| **Availability** | [X]% | >[Y]% | Healthy / Degraded |
| **TTFT (Streaming)** | [X]ms | <[Y]ms | Healthy / Degraded |

### 1.4 Agent-Specific Metrics

| Agent | Success Rate | Avg Latency | Error Rate | Fallback Rate |
|-------|--------------|-------------|------------|---------------|
| [Agent 1] | [X]% | [X]ms | [X]% | [X]% |
| [Agent 2] | [X]% | [X]ms | [X]% | [X]% |
| [Agent 3] | [X]% | [X]ms | [X]% | [X]% |

**Observability Stack**: [OpenTelemetry | Datadog | Custom | etc.]

---

## 2. Feedback Loops

### 2.1 User Feedback Collection

| Channel | What We Collect | Frequency | Owner |
|---------|-----------------|-----------|-------|
| In-app feedback | [Thumbs up/down, text feedback] | Continuous | [Team] |
| Support tickets | [Bug reports, feature requests] | Continuous | [Team] |
| User interviews | [Deep qualitative insights] | [Monthly/Quarterly] | [Team] |
| Surveys | [NPS, satisfaction] | [Quarterly] | [Team] |

### 2.2 Implicit Feedback Signals

| Signal | What It Indicates | How We Use It |
|--------|-------------------|---------------|
| [Session length] | [Engagement depth] | [Model training signal] |
| [Task completion rate] | [Success rate] | [Quality metric] |
| [Retry rate] | [User frustration] | [Error detection] |
| [Feature adoption] | [Value delivery] | [Prioritization] |

### 2.3 Feedback Processing

**Weekly Feedback Review**:
- [ ] Aggregate feedback by category
- [ ] Identify top 3 pain points
- [ ] Check for safety-related reports
- [ ] Update backlog with user insights
- [ ] Close loop with reporters (if identifiable)

**Feedback → Action Pipeline**:

```
Raw Feedback → Categorize → Prioritize → Create Issue → Implement → Validate → Close Loop
     │              │            │
     └── Safety ────┴── Urgent ──┴── Immediate escalation
```

### 2.4 Trusted Flaggers Program

For applications with safety considerations:

| Role | Access | Responsibility |
|------|--------|----------------|
| Internal QA | Full system access | Daily monitoring, edge case testing |
| Domain experts | Escalation queue | Complex case review |
| External advisors | Periodic review | Ethical/safety audit |

---

## 3. Model Maintenance

### 3.1 Retraining Triggers

| Trigger | Threshold | Action |
|---------|-----------|--------|
| **Performance Drift** | [Metric] drops [X]% | Investigate, consider retraining |
| **Data Drift** | Distribution shift detected | Evaluate model on new data |
| **New Capability Needed** | Feature request validated | Fine-tune or prompt engineering |
| **Safety Issue** | Any confirmed | Immediate review |
| **Calendar** | [Quarterly/Monthly] | Scheduled evaluation |

### 3.2 Model Evaluation Cadence

| Evaluation Type | Frequency | What We Check |
|-----------------|-----------|---------------|
| **Spot checks** | Daily | Sample outputs for quality |
| **Benchmark suite** | Weekly | Standard test cases |
| **Red teaming** | Monthly | Adversarial testing |
| **Full evaluation** | Quarterly | Comprehensive metrics review |

### 3.3 A/B Testing Framework

**Current Experiments**:

| Experiment | Hypothesis | Metric | Status | Results |
|------------|------------|--------|--------|---------|
| [Experiment 1] | [What we're testing] | [Primary metric] | Running / Complete | [Outcome] |

**A/B Testing Rules**:
- Minimum sample size: [N users or N requests]
- Statistical significance: [p < 0.05]
- Minimum duration: [X days]
- Guardrail metrics: [Metrics that must not degrade]

### 3.4 Rollback Procedures

**When to Rollback**:
- [ ] Error rate exceeds [X]%
- [ ] Latency P95 exceeds [X]ms
- [ ] Safety incident confirmed
- [ ] North Star metric drops [X]% in [timeframe]

**Rollback Process**:

```
1. DETECT: Automated alert OR manual report
2. VERIFY: Confirm issue is model-related (not infra)
3. DECIDE: On-call makes rollback decision
4. EXECUTE: Deploy previous model version
5. VALIDATE: Confirm metrics recovered
6. INVESTIGATE: Root cause analysis
7. DOCUMENT: Update ADR with findings
```

**Rollback SLA**: [X minutes] from detection to stable state

---

## 4. Incident Response

### 4.1 Severity Levels

| Level | Definition | Response Time | Examples |
|-------|------------|---------------|----------|
| **SEV1** | Complete outage or safety incident | 15 min | System down, harmful output |
| **SEV2** | Major degradation | 1 hour | 50%+ error rate, severe latency |
| **SEV3** | Minor degradation | 4 hours | Feature broken, elevated errors |
| **SEV4** | Cosmetic or minor | Next business day | UI glitch, typo |

### 4.2 On-Call Rotation

| Role | Responsibility | Contact |
|------|----------------|---------|
| Primary on-call | First responder, initial triage | [Contact method] |
| Secondary on-call | Escalation, backup | [Contact method] |
| Incident commander | SEV1/SEV2 coordination | [Contact method] |

**Rotation Schedule**: [Link to schedule]

### 4.3 Incident Response Checklist

**SEV1/SEV2 Response**:
- [ ] Acknowledge alert within [X minutes]
- [ ] Create incident channel/ticket
- [ ] Assess impact and scope
- [ ] Decide: mitigate vs rollback
- [ ] Communicate status to stakeholders
- [ ] Implement fix or rollback
- [ ] Validate resolution
- [ ] Schedule post-incident review

### 4.4 Post-Incident Review Template

```
Incident: [Title]
Severity: [SEV1-4]
Duration: [Start - End]
Impact: [Users affected, scope]

Timeline:
- [Time]: [Event]
- [Time]: [Event]

Root Cause:
[What caused the incident]

Contributing Factors:
- [Factor 1]
- [Factor 2]

What Went Well:
- [Positive 1]
- [Positive 2]

What Needs Improvement:
- [Gap 1]
- [Gap 2]

Action Items:
- [ ] [Action] — Owner: [Name] — Due: [Date]
- [ ] [Action] — Owner: [Name] — Due: [Date]
```

---

## 5. Security & Compliance

### 5.1 Security Review Cadence

| Review Type | Frequency | Scope | Owner |
|-------------|-----------|-------|-------|
| Vulnerability scan | Weekly | Infrastructure | [Team] |
| Dependency audit | Monthly | Package vulnerabilities | [Team] |
| Access review | Quarterly | User/service permissions | [Team] |
| Penetration test | Annually | Full system | [Team] |
| Red team exercise | Annually | Adversarial AI testing | [Team] |

### 5.2 Compliance Checklist

| Requirement | Status | Last Verified | Next Review |
|-------------|--------|---------------|-------------|
| [GDPR/CCPA compliance] | Compliant / Gap | [Date] | [Date] |
| [Data retention policy] | Compliant / Gap | [Date] | [Date] |
| [Access controls] | Compliant / Gap | [Date] | [Date] |
| [Audit logging] | Compliant / Gap | [Date] | [Date] |
| [Incident disclosure] | Compliant / Gap | [Date] | [Date] |

### 5.3 Privacy Operations

**Data Subject Requests**:

| Request Type | SLA | Process |
|--------------|-----|---------|
| Access (DSAR) | [X days] | [Link to process] |
| Deletion | [X days] | [Link to process] |
| Correction | [X days] | [Link to process] |
| Export | [X days] | [Link to process] |

**Data Retention**:

| Data Type | Retention Period | Deletion Method |
|-----------|------------------|-----------------|
| [User sessions] | [X days/months] | [Automated/Manual] |
| [Conversation logs] | [X days/months] | [Automated/Manual] |
| [Analytics] | [X months/years] | [Automated/Manual] |

---

## 6. Cost & Resource Tracking

### 6.1 Monthly Cost Review

| Cost Category | Last Month | This Month | Trend | Budget |
|---------------|------------|------------|-------|--------|
| **LLM API calls** | $[X] | $[X] | ↑↓ | $[X] |
| **Infrastructure** | $[X] | $[X] | ↑↓ | $[X] |
| **Database** | $[X] | $[X] | ↑↓ | $[X] |
| **Observability** | $[X] | $[X] | ↑↓ | $[X] |
| **Total** | $[X] | $[X] | ↑↓ | $[X] |

### 6.2 Unit Economics

| Metric | Current | Target |
|--------|---------|--------|
| Cost per user | $[X] | $[X] |
| Cost per request | $[X] | $[X] |
| Cost per successful outcome | $[X] | $[X] |

### 6.3 Cost Optimization Levers

| Lever | Current State | Optimization Opportunity |
|-------|---------------|--------------------------|
| Model selection | [Current models] | [Cheaper model for X tasks] |
| Caching | [Cache hit rate]% | [Target hit rate]% |
| Batching | [Current approach] | [Batch X requests] |
| Prompt optimization | [Current token usage] | [Reduce by X%] |

### 6.4 Auto-Scaling Configuration

| Resource | Min | Max | Scale-Up Trigger | Scale-Down Trigger |
|----------|-----|-----|------------------|-------------------|
| [Service 1] | [N] | [N] | [Condition] | [Condition] |
| [Service 2] | [N] | [N] | [Condition] | [Condition] |

---

## 7. Iteration Planning

### 7.1 Quarterly Strategy Review

**Review Questions**:
- [ ] Is the North Star metric still the right metric?
- [ ] Are we hitting phase targets?
- [ ] What did we learn from user feedback?
- [ ] Should anything move to/from the kill list?
- [ ] Do we need to pivot?

**Review Artifacts**:
- [ ] Update NORTHSTAR.md with learnings
- [ ] Create ADRs for strategy changes
- [ ] Update phase boundaries if needed
- [ ] Communicate changes to stakeholders

### 7.2 Kill List Maintenance

**Quarterly Kill List Review**:

| Item | Original Rationale | New Evidence | Decision |
|------|-------------------|--------------|----------|
| [Kill list item] | [Why rejected] | [Any new info] | Keep / Reconsider |

**Adding to Kill List**:
- Document in ADR why it's being killed
- Update NORTHSTAR.md kill list
- Update NORTHSTAR_EXTRACT.md non-goals
- Communicate to team

### 7.3 Roadmap Updates

**Next Quarter Priorities**:

| Priority | Initiative | Success Metric | Owner |
|----------|------------|----------------|-------|
| P0 | [Initiative] | [Metric] | [Owner] |
| P1 | [Initiative] | [Metric] | [Owner] |
| P2 | [Initiative] | [Metric] | [Owner] |

**Backlog Health**:

| Category | Count | Oldest Item |
|----------|-------|-------------|
| Bugs | [N] | [Age] |
| Features | [N] | [Age] |
| Tech debt | [N] | [Age] |
| Safety | [N] | [Age] |

### 7.4 ADR Updates

**Recent Decisions** (link to ADR.md):

| Date | Decision | Status |
|------|----------|--------|
| [Date] | [Decision title] | Implemented / Pending |

**Decisions to Document**:
- [ ] [Pending decision 1]
- [ ] [Pending decision 2]

---

## 8. Runbook Quick Reference

### Daily Checklist

- [ ] Check system health dashboard
- [ ] Review overnight alerts
- [ ] Spot check sample outputs
- [ ] Process urgent feedback

### Weekly Checklist

- [ ] North Star metric review
- [ ] Input metrics review
- [ ] Feedback aggregation
- [ ] Agent performance review
- [ ] Cost tracking update

### Monthly Checklist

- [ ] Full cost review
- [ ] Security scan review
- [ ] Model evaluation
- [ ] Backlog grooming
- [ ] On-call retrospective

### Quarterly Checklist

- [ ] Strategy review
- [ ] Kill list review
- [ ] Compliance audit
- [ ] Access review
- [ ] Roadmap planning
- [ ] ADR updates

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Fill in monitoring thresholds based on your NORTHSTAR.md targets
2. Define feedback collection channels for your user base
3. Set model maintenance triggers appropriate to your scale
4. Configure incident severity levels for your risk tolerance
5. Document compliance requirements for your jurisdiction
6. Set cost budgets based on your unit economics
7. Establish review cadences that fit your team size

**Good post-deployment operations:**

- Tied directly to North Star metric
- Automated where possible, human-reviewed where needed
- Clear ownership for every process
- Defined escalation paths
- Regular review and iteration

---

## Validation Schema (For AI Generation)

```yaml
# POST_DEPLOYMENT validation gate
inputs_required:
  - northstar.metric: from_northstar
  - northstar.input_metrics[]: from_northstar
  - arch.agent_topology[]: from_architecture_blueprint
  - arch.latency_budget: from_architecture_blueprint
  - security.audit_requirements: from_security_architecture

outputs_produced:
  - ops.monitoring_dashboard: used_by_operations
  - ops.feedback_loops: used_by_iteration
  - ops.incident_response: used_by_on_call
  - ops.cost_tracking: used_by_finance
  - ops.review_cadences: used_by_team

validation_gate:
  required_sections:
    - "Monitoring Dashboard"
    - "Feedback Loops"
    - "Model Maintenance"
    - "Incident Response"
    - "Cost & Resource Tracking"

  minimum_content:
    health_metrics: 3
    severity_levels: 3
    review_cadences: 4
    cost_categories: 3

  placeholders_filled:
    - "[PROJECT NAME]"
    - "[Your Metric Name]"
    - "[DATE]"

cross_references:
  - monitoring_metrics: must_include: northstar.metric
  - monitoring_metrics: must_include: northstar.input_metrics
  - agent_metrics: must_cover: arch.agent_topology
  - latency_slos: must_align_with: arch.latency_budget

quality_checks:
  - each_metric: has_threshold_and_action
  - each_severity: has_response_time
  - each_review: has_owner_and_cadence
  - incident_response: has_rollback_procedure

decision_prompts:
  - "What latency SLOs are acceptable for your users?"
  - "Who is on-call and what's the escalation path?"
  - "What's your monthly budget for LLM costs?"
  - "How often should you review the North Star metric?"
```

**After generation, verify:**
- [ ] No `[PLACEHOLDER]` text remains
- [ ] Monitoring metrics include North Star and all input metrics
- [ ] Agent metrics cover all agents in topology
- [ ] Latency SLOs align with architecture latency budget
- [ ] Incident severity levels have clear definitions and response times
- [ ] Cost categories cover major expense drivers
- [ ] Review cadences have owners assigned

---

© [YEAR] [AUTHOR/ORG]
