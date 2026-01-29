# [PROJECT NAME]: Operations Runbook

> **Parent**: [INDEX.md](INDEX.md)
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]

How to operate and maintain your agentic system in production.

---

## 1. Health Checks

### What to Monitor

| Check | Healthy | Degraded | Critical |
|-------|---------|----------|----------|
| Pipeline latency P95 | <[X]s | <[Y]s | >[Y]s |
| Agent fallback rate | <5% | <15% | >15% |
| Circuit breakers open | 0 | 1-2 | >2 |
| Error rate | <1% | <5% | >5% |

### Daily Review

- [ ] Check fallback rates per agent
- [ ] Review circuit breaker states
- [ ] Verify cost within budget

---

## 2. Common Issues

### Agent Timing Out

**Symptoms**: Fallback responses, increased latency

**Check**:
1. Is the LLM provider healthy?
2. Are token budgets appropriate?
3. Is the circuit breaker open?

**Fix**: [Your remediation steps]

---

### High Fallback Rate

**Symptoms**: Generic responses, low user satisfaction

**Check**:
1. Which agent is falling back most?
2. Is the model returning valid JSON?
3. Are prompts within token limits?

**Fix**: [Your remediation steps]

---

### Safety Agent Alerting

**Symptoms**: High severity flags, escalation triggers

**Check**:
1. Review flagged conversations
2. Verify crisis resources are current
3. Confirm escalation paths work

**Respond**: [Your escalation procedure]

---

## 3. Scaling

### When to Scale

| Signal | Action |
|--------|--------|
| Latency P95 consistently >[X]s | Add capacity |
| Cost per request rising | Optimize prompts or model selection |
| Queue depth growing | Add workers |

---

## 4. Rollback

### Before Deploying

- [ ] Note current version: `___________`
- [ ] Verify rollback tested in staging
- [ ] Confirm monitoring is active

### If Something Goes Wrong

1. **Pause**: Stop new traffic if possible
2. **Assess**: Check error rates and logs
3. **Rollback**: Revert to previous known-good version
4. **Verify**: Confirm system healthy
5. **Document**: Record what happened in ADR

---

## 5. Contacts

| Role | Contact | When |
|------|---------|------|
| On-call | [Contact] | First responder |
| Escalation | [Contact] | If on-call can't resolve |
| LLM Provider | [Support link] | Provider-side issues |

---

## Template Notes

Delete this section after completing the template.

**Start small.** Add runbook entries as you encounter real issues.

**Good runbooks**:
- Are written after incidents, not before
- Include actual commands and steps
- Stay current (outdated runbooks are dangerous)

---

## Validation Schema (For AI Generation)

```yaml
# OPERATIONS_RUNBOOK validation gate
inputs_required:
  - arch.agent_topology[]: from_architecture_blueprint
  - arch.latency_budget: from_architecture_blueprint
  - security.escalation_rules: from_security_architecture

outputs_produced:
  - ops.health_checks: used_by_post_deployment
  - ops.common_issues: used_by_on_call
  - ops.rollback_procedure: used_by_incident_response
  - ops.contacts: used_by_on_call

validation_gate:
  required_sections:
    - "Health Checks"
    - "Common Issues"
    - "Emergency Procedures"
    - "Contacts"

  minimum_content:
    health_checks: 3
    common_issues: 2
    emergency_procedures: 1
    contacts: 2

cross_references:
  - health_checks: must_cover: arch.agent_topology
  - latency_thresholds: must_align_with: arch.latency_budget

quality_checks:
  - health_checks: have_thresholds_and_actions
  - common_issues: have_symptoms_and_fixes
  - emergency_procedures: have_step_by_step_commands

decision_prompts:
  - "What are the most likely failure modes?"
  - "Who is on-call and how are they contacted?"
  - "What's the rollback procedure?"
```

**After generation, verify:**
- [ ] Health checks cover all agents in topology
- [ ] Common issues have symptoms and fixes
- [ ] Emergency procedures include rollback steps
- [ ] Contact list has on-call and escalation paths

---

© [YEAR] [AUTHOR/ORG]
