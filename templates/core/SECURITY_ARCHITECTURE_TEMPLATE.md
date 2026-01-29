# [PROJECT NAME]: Security Architecture

<!-- GENERATION: This is Step 8 of 13. Requires outputs from ARCHITECTURE_BLUEPRINT and BRAND_GUIDELINES. See GENERATION_MANIFEST.md -->

> **Tier**: 2 — Implementation (see [INDEX.md](INDEX.md))
> **Created**: [DATE]
> **Status**: [Draft | Active | Archived]
> **Generation Step**: 8 of 13 — Requires `brand.license`, `arch.agent_topology[]`, `extract.axioms[]`
> **Scope**: Authentication, Authorization, Data Protection, Safety

*Security architecture for [brief description of your system]*

---

## Executive Summary

[PROJECT NAME] presents [unique security challenge description]. Traditional application security is insufficient—[PROJECT NAME] requires **agentic-native security** designed for autonomous AI operating in [your domain context].

This security blueprint addresses the **OWASP Top 10 for Agentic Applications** and implements defense-in-depth across the [N]-agent architecture.

---

## 1. Threat Model

### 1.1 Attack Surface Analysis

```
┌────────────────────────────────────────────────────────────────────────────┐
│                    [PROJECT NAME] ATTACK SURFACE MAP                        │
├────────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│  EXTERNAL THREATS                    INTERNAL THREATS                      │
│  ────────────────                    ────────────────                      │
│  ┌─────────────┐                     ┌─────────────┐                       │
│  │   USER      │ ──────────────────▶ │  PROMPT     │                       │
│  │   INPUT     │   Direct Injection  │  INJECTION  │                       │
│  └─────────────┘                     └─────────────┘                       │
│         │                                   │                              │
│         │  Indirect Injection               ▼                              │
│         │  (via [vector])            ┌─────────────┐                       │
│         └──────────────────────────▶ │  AGENT      │                       │
│                                      │  HIJACKING  │                       │
│  ┌─────────────┐                     └─────────────┘                       │
│  │  [VECTOR 1] │ ─────────────────────────▶ │                              │
│  │  POISONING  │   Persistent Injection     ▼                              │
│  └─────────────┘                     ┌─────────────┐                       │
│                                      │ PRIVILEGE   │                       │
│  ┌─────────────┐                     │ ESCALATION  │                       │
│  │  INTER-     │ ◀────────────────── └─────────────┘                       │
│  │  AGENT      │   Spoofed Messages          │                             │
│  │  ATTACK     │                             ▼                             │
│  └─────────────┘                     ┌─────────────┐                       │
│                                      │ DATA        │                       │
│  ┌─────────────┐                     │ EXFIL       │                       │
│  │  CREDENTIAL │ ◀───────────────────└─────────────┘                       │
│  │  THEFT      │   Leaked API Keys                                         │
│  └─────────────┘                                                           │
│                                                                            │
│  SENSITIVE ASSETS AT RISK                                                  │
│  ────────────────────────                                                  │
│  • [Asset 1] (sensitivity level)                                           │
│  • [Asset 2] (sensitivity level)                                           │
│  • [Asset 3] (sensitivity level)                                           │
│  • API credentials                                                         │
│  • User identity tokens                                                    │
│                                                                            │
└────────────────────────────────────────────────────────────────────────────┘
```

### 1.2 OWASP Agentic AI Top 10 Risk Mapping

| OWASP Risk ID | Risk Name | Your Exposure | Severity | Priority |
|---------------|-----------|---------------|----------|----------|
| **ASI01** | Agent Goal Hijack | [Exposure level] - [Description] | [Critical/High/Medium] | P[0-2] |
| **ASI02** | Tool Misuse & Exploitation | [Exposure level] - [Description] | [Critical/High/Medium] | P[0-2] |
| **ASI03** | Identity & Privilege Abuse | [Exposure level] - [Description] | [Critical/High/Medium] | P[0-2] |
| **ASI04** | Agentic Supply Chain | [Exposure level] - [Description] | [Critical/High/Medium] | P[0-2] |
| **ASI05** | Unexpected Code Execution | [Exposure level] - [Description] | [Critical/High/Medium] | P[0-2] |
| **ASI06** | Memory & Context Poisoning | [Exposure level] - [Description] | [Critical/High/Medium] | P[0-2] |
| **ASI07** | Insecure Inter-Agent Communication | [Exposure level] - [Description] | [Critical/High/Medium] | P[0-2] |
| **ASI08** | Cascading Failures | [Exposure level] - [Description] | [Critical/High/Medium] | P[0-2] |
| **ASI09** | Human-Agent Trust Exploitation | [Exposure level] - [Description] | [Critical/High/Medium] | P[0-2] |
| **ASI10** | Rogue Agents | [Exposure level] - [Description] | [Critical/High/Medium] | P[0-2] |

### 1.3 Trust Boundaries

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          TRUST BOUNDARY MAP                                 │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  UNTRUSTED ZONE (TB0)           SEMI-TRUSTED ZONE (TB1)                     │
│  ──────────────────────          ─────────────────────                      │
│  ┌─────────────────────┐        ┌─────────────────────┐                     │
│  │                     │        │                     │                     │
│  │  • User input       │───────▶│  • Gateway Layer    │                     │
│  │  • External content │  TB0   │  • Input validation │                     │
│  │  • Retrieved docs   │        │  • Rate limiting    │                     │
│  │                     │        │                     │                     │
│  └─────────────────────┘        └──────────┬──────────┘                     │
│                                            │                                │
│                                      ──────┼────── TB1                      │
│                                            ▼                                │
│  TRUSTED ZONE (TB2)               [AGENT CLUSTER 1]                         │
│  ─────────────────────           ┌─────────────────────┐                    │
│  ┌─────────────────────┐         │  • [Agent 1]        │                    │
│  │                     │◀────────│  • [Agent 2]        │                    │
│  │  • System prompts   │   TB2   │  • [Agent 3]        │                    │
│  │  • Configuration    │         └─────────────────────┘                    │
│  │  • Agent contracts  │                   │                                │
│  │                     │            ───────┼────── TB2                      │
│  └─────────────────────┘                   ▼                                │
│                                  [AGENT CLUSTER 2]                          │
│  PRIVILEGED ZONE (TB3)          ┌─────────────────────┐                     │
│  ───────────────────────         │  • [Agent 4]        │                    │
│  ┌─────────────────────┐         │  • [Agent 5]        │                    │
│  │                     │◀────────└─────────────────────┘                    │
│  │  • Governance layer │   TB3                                              │
│  │  • API credentials  │         ┌─────────────────────┐                    │
│  │  • Audit logs       │◀────────│  GOVERNANCE LAYER   │                    │
│  │  • Kill switches    │   TB3   │  (Always monitoring)│                    │
│  │                     │         └─────────────────────┘                    │
│  └─────────────────────┘                                                    │
│                                                                             │
│  TRUST BOUNDARY ENFORCEMENT:                                                │
│  • TB0→TB1: Input sanitization, prompt injection filtering                  │
│  • TB1→TB2: Validated data structures, no raw user strings                  │
│  • TB2→TB3: Mutual authentication, signed messages, audit trail             │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 1.4 Threat Scenarios

#### Scenario 1: [Attack Name] (ASI[XX])
**Attacker Goal**: [What the attacker wants to achieve]
**Attack Vector**: [How the attack is carried out]
**Example**: "[Concrete example of the attack]"
**Impact**: [Consequences of successful attack]

#### Scenario 2: [Attack Name] (ASI[XX])
**Attacker Goal**: [What the attacker wants to achieve]
**Attack Vector**: [How the attack is carried out]
**Example**: "[Concrete example of the attack]"
**Impact**: [Consequences of successful attack]

#### Scenario 3: [Attack Name] (ASI[XX])
**Attacker Goal**: [What the attacker wants to achieve]
**Attack Vector**: [How the attack is carried out]
**Example**: "[Concrete example of the attack]"
**Impact**: [Consequences of successful attack]

---

## 2. Authentication Architecture

### 2.1 Identity Model

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    [PROJECT NAME] IDENTITY ARCHITECTURE                      │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  USER IDENTITY                    AGENT IDENTITY                            │
│  ─────────────                    ──────────────                            │
│  ┌─────────────────┐              ┌───────────────────┐                     │
│  │  User           │              │  Agent            │                     │
│  │  ┌───────────┐  │              │  ┌─────────────┐  │                     │
│  │  │ user_id   │  │              │  │ agent_id    │  │                     │
│  │  │ session_id│  │              │  │ cluster     │  │                     │
│  │  │ token     │  │              │  │ role        │  │                     │
│  │  │ consent   │  │              │  │ credentials │  │                     │
│  │  └───────────┘  │              │  │ capabilities│  │                     │
│  │                 │              │  └─────────────┘  │                     │
│  └─────────────────┘              └───────────────────┘                     │
│                                                                             │
│  SESSION IDENTITY                 SERVICE IDENTITY                          │
│  ────────────────                 ────────────────                          │
│  ┌─────────────────┐              ┌─────────────────┐                       │
│  │  Session        │              │  External Svc   │                       │
│  │  ┌───────────┐  │              │  ┌───────────┐  │                       │
│  │  │ session_id│  │              │  │ service_id│  │                       │
│  │  │ created_at│  │              │  │ api_key   │  │                       │
│  │  │ expires_at│  │              │  │ scope     │  │                       │
│  │  │ state     │  │              │  │ rate_limit│  │                       │
│  │  │ agent_ctx │  │              │  └───────────┘  │                       │
│  │  └───────────┘  │              └─────────────────┘                       │
│  └─────────────────┘                                                        │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 2.2 Agent Authentication (Zero-Trust)

> **Note**: Adapt to your chosen language.

```
// src/security/agent-auth

AgentCredential:
  agentId: string
  privateKey: bytes
  publicKey: bytes
  capabilities: list of strings
  tokenBudget: number
  expiresAt: timestamp

AgentToken:
  agentId: string
  sessionId: string
  issuedAt: timestamp
  expiresAt: timestamp
  capabilities: list of strings
  signature: bytes

class AgentAuthenticator:
  registry: map of agentId to AgentCredential

  authenticateAgent(agentId, signature, message) -> boolean:
    // Verify agent identity using cryptographic signature

  issueSessionToken(agentId, sessionId) -> AgentToken:
    // Issue short-lived token for session participation

  validateToken(token) -> boolean:
    // Validate token is current and properly signed
```

### 2.3 Agent Capability Definitions

> **Note**: Adapt to your chosen language.

```
// src/security/capabilities

AGENT_CAPABILITIES: map of agentId to list of permissions

  "[agent_1]": [
    "read:user_input",
    "write:parsed_output"
  ]

  "[agent_2]": [
    "read:parsed_input",
    "read:session_metadata",
    "write:agent_output"
  ]

  "[safety_agent]": [
    "read:*",  // Can read all for safety checks
    "write:safety_decision",
    "execute:block_response",
    "execute:escalate"
  ]

  // ... define for all agents
```

### 2.4 User Authentication

**Provider**: [Your auth provider]

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      AUTHENTICATION FLOW                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  1. User signs in via [provider] UI                                         │
│       │                                                                     │
│       ▼                                                                     │
│  2. [Provider] validates credentials, issues JWT                            │
│       │                                                                     │
│       ▼                                                                     │
│  3. JWT included in API requests                                            │
│       │                                                                     │
│       ▼                                                                     │
│  4. Gateway validates JWT, extracts user_id                                 │
│       │                                                                     │
│       ▼                                                                     │
│  5. Session created/resumed with user context                               │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 3. Authorization Matrix

### 3.1 Role-Based Access Control (RBAC)

| Role | Description | Permissions |
|------|-------------|-------------|
| **Guest** | Unauthenticated user | Read public content only |
| **User** | Authenticated user | Full interaction within own sessions |
| **Admin** | System administrator | Configuration, monitoring, override |
| **Agent** | Autonomous agent | Scoped per agent_id |

### 3.2 Agent Authorization Matrix

| Agent | Read | Write | Execute | Delete |
|-------|------|-------|---------|--------|
| `[agent_1]` | user_input | parsed_output | — | — |
| `[agent_2]` | parsed_input, context | agent_output | — | — |
| `[agent_3]` | * | safety_decision | block, escalate | — |
| `[agent_4]` | agent_outputs | final_state | — | — |
| `[agent_5]` | final_state | response | — | — |

### 3.3 Data Access Controls

| Data Type | Access Level | Retention | Encryption |
|-----------|--------------|-----------|------------|
| User input | User + Agents | [Period] | AES-256 |
| Session state | User + Session agents | [Period] | AES-256 |
| Long-term memory | User only | [Period] | AES-256 + user key |
| Audit logs | Admin only | [Period] | AES-256 |

---

## 4. Audit System

### 4.1 Audit Event Types

| Event Category | Events | Retention |
|----------------|--------|-----------|
| **Authentication** | login, logout, token_refresh, auth_failure | 90 days |
| **Authorization** | permission_check, access_denied, escalation | 90 days |
| **Agent Activity** | agent_start, agent_complete, agent_error | 30 days |
| **Data Access** | read, write, delete, export | 1 year |
| **Safety Events** | content_blocked, escalation, human_override | 2 years |

### 4.2 Audit Log Schema

> **Note**: Adapt to your chosen language.

```
// src/security/audit

AuditEvent:
  eventId: string           // UUIDv7
  timestamp: number         // Unix milliseconds
  eventType: string         // e.g., "auth.login"
  actor:
    type: "user" | "agent" | "system"
    id: string
  target (optional):
    type: string
    id: string
  action: string
  outcome: "success" | "failure" | "partial"
  context:
    sessionId: string (optional)
    requestId: string (optional)
    ip: string (optional)
    userAgent: string (optional)
  metadata: map of key-value pairs  // Event-specific data
```

### 4.3 Audit Query Interface

> **Note**: Adapt to your chosen language.

```
// src/security/audit

AuditQuery:
  startTime: number (optional)
  endTime: number (optional)
  eventTypes: list of strings (optional)
  actorId: string (optional)
  targetId: string (optional)
  outcome: "success" | "failure" (optional)
  limit: number (optional)
  offset: number (optional)

queryAuditLogs(query) -> list of AuditEvent:
  // Query implementation
```

---

## 5. Resilience Safeguards

### 5.1 Circuit Breakers

| Component | Failure Threshold | Recovery Time | Fallback |
|-----------|------------------|---------------|----------|
| [LLM Provider] | 5 failures / 60s | 30s | [Fallback provider] |
| [Agent 1] | 3 failures / 30s | 15s | [Template response] |
| [Database] | 2 failures / 30s | 60s | [Cache fallback] |

### 5.2 Kill Switches

> **Note**: Adapt to your chosen language.

```
// src/security/kill-switches

KillSwitch:
  id: string
  scope: "global" | "agent" | "user" | "feature"
  targetId: string (optional)
  reason: string
  enabledAt: timestamp
  enabledBy: string

KILL_SWITCHES:
  GLOBAL_SHUTDOWN:
    id: "global_shutdown"
    scope: "global"
    description: "Immediately halt all agent processing"

  AGENT_DISABLE:
    id: "agent_disable"
    scope: "agent"
    description: "Disable specific agent from processing"

  USER_BLOCK:
    id: "user_block"
    scope: "user"
    description: "Block specific user from system access"
```

### 5.3 Rate Limiting

| Endpoint | Rate Limit | Window | Burst |
|----------|-----------|--------|-------|
| `/api/[endpoint]` | 100 req | 1 min | 10 |
| `/api/[streaming]` | 20 req | 1 min | 5 |
| `/api/admin/*` | 10 req | 1 min | 2 |

---

## 6. Human Escalation Rules

### 6.1 Automatic Escalation Triggers

| Trigger | Condition | Action |
|---------|-----------|--------|
| **Safety severity high** | severity >= 4 | Notify on-call |
| **Repeated safety flags** | 3+ in 24h for same user | Review queue |
| **User request** | User explicitly asks for human | Support ticket |
| **System anomaly** | Unusual patterns detected | Alert + log |

### 6.2 Escalation Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         HUMAN ESCALATION FLOW                               │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  TRIGGER DETECTED                                                           │
│       │                                                                     │
│       ▼                                                                     │
│  ┌─────────────┐    Yes    ┌─────────────┐                                  │
│  │ Immediate   │──────────▶│ Page        │                                  │
│  │ danger?     │           │ On-call     │                                  │
│  └─────────────┘           └─────────────┘                                  │
│       │ No                                                                  │
│       ▼                                                                     │
│  ┌─────────────┐    Yes    ┌─────────────┐                                  │
│  │ Safety      │──────────▶│ Support     │                                  │
│  │ concern?    │           │ Queue       │                                  │
│  └─────────────┘           └─────────────┘                                  │
│       │ No                                                                  │
│       ▼                                                                     │
│  ┌─────────────┐    Yes    ┌─────────────┐                                  │
│  │ User        │──────────▶│ Standard    │                                  │
│  │ request?    │           │ Support     │                                  │
│  └─────────────┘           └─────────────┘                                  │
│       │ No                                                                  │
│       ▼                                                                     │
│  ┌─────────────┐                                                            │
│  │ Log &       │                                                            │
│  │ Continue    │                                                            │
│  └─────────────┘                                                            │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 6.3 Response Templates

| Situation | Response |
|-----------|----------|
| Safety escalation | "[Compassionate acknowledgment]. A team member will follow up within [timeframe]." |
| System issue | "I'm experiencing some difficulties. Let me connect you with support." |
| User request | "I'll connect you with a human team member. They'll be with you shortly." |

---

## 7. Security Checklist

### Pre-Launch Checklist

#### Authentication & Authorization
- [ ] All API endpoints require authentication
- [ ] JWT validation implemented and tested
- [ ] Agent credentials rotated and securely stored
- [ ] Capability enforcement verified per agent
- [ ] Session timeout implemented

#### Data Protection
- [ ] All sensitive data encrypted at rest
- [ ] TLS 1.3 for all connections
- [ ] PII sanitization in logs verified
- [ ] Data retention policies configured
- [ ] Backup encryption verified

#### Threat Mitigation
- [ ] Prompt injection filters deployed
- [ ] Rate limiting configured
- [ ] Circuit breakers tested
- [ ] Kill switches operational
- [ ] Input validation comprehensive

#### Monitoring & Response
- [ ] Audit logging enabled
- [ ] Alert thresholds configured
- [ ] Escalation contacts verified
- [ ] Incident runbooks documented
- [ ] Security metrics dashboard live

### Post-Launch Monitoring

- [ ] Review security alerts daily
- [ ] Audit log analysis weekly
- [ ] Penetration testing quarterly
- [ ] Dependency vulnerability scan monthly
- [ ] Security training annually

---

## 8. Incident Response Playbook

### Severity Levels

| Level | Description | Response Time | Examples |
|-------|-------------|---------------|----------|
| **SEV-1** | Critical - Active exploitation | 15 min | Data breach, complete outage |
| **SEV-2** | High - Significant risk | 1 hour | Partial breach, degraded security |
| **SEV-3** | Medium - Potential risk | 4 hours | Suspicious activity, failed attack |
| **SEV-4** | Low - Minor issue | 24 hours | Policy violation, warning |

### Response Procedures

#### SEV-1: Critical Incident

1. **Immediate Actions** (0-15 min)
   - Activate incident commander
   - Enable relevant kill switches
   - Notify security team
   - Begin evidence preservation

2. **Containment** (15-60 min)
   - Isolate affected systems
   - Block attacker access
   - Assess scope of impact

3. **Eradication** (1-4 hours)
   - Remove attacker presence
   - Patch vulnerability
   - Reset compromised credentials

4. **Recovery** (4-24 hours)
   - Restore services gradually
   - Verify integrity
   - Enhanced monitoring

5. **Post-Incident** (24-72 hours)
   - Root cause analysis
   - Lessons learned document
   - Preventive measures

### Communication Templates

```markdown
# Incident Notification Template

**Severity**: [SEV-1/2/3/4]
**Status**: [Investigating | Contained | Resolved]
**Impact**: [Description of user impact]
**Timeline**: [When detected, current status, ETA for resolution]
**Actions**: [What we're doing]
**User Action Required**: [If any]
```

---

## 9. Security Architecture Summary

### Defense in Depth

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         DEFENSE IN DEPTH                                    │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  Layer 1: PERIMETER                                                         │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  • WAF / DDoS protection                                            │    │
│  │  • Rate limiting                                                    │    │
│  │  • TLS 1.3 enforcement                                              │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  Layer 2: AUTHENTICATION                                                    │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  • JWT validation                                                   │    │
│  │  • Agent credential verification                                    │    │
│  │  • Session management                                               │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  Layer 3: AUTHORIZATION                                                     │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  • RBAC enforcement                                                 │    │
│  │  • Agent capability checks                                          │    │
│  │  • Data access controls                                             │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  Layer 4: INPUT VALIDATION                                                  │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  • Prompt injection filtering                                       │    │
│  │  • Schema validation                                                │    │
│  │  • Sanitization                                                     │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  Layer 5: MONITORING                                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  • Audit logging                                                    │    │
│  │  • Anomaly detection                                                │    │
│  │  • Alert management                                                 │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
│  Layer 6: RESILIENCE                                                        │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  • Circuit breakers                                                 │    │
│  │  • Kill switches                                                    │    │
│  │  • Graceful degradation                                             │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Key Security Principles

| Principle | Implementation |
|-----------|----------------|
| **Zero Trust** | All agents authenticate; no implicit trust |
| **Least Privilege** | Agents have minimum required capabilities |
| **Defense in Depth** | Multiple security layers |
| **Fail Secure** | Errors result in denial, not access |
| **Audit Everything** | Comprehensive logging for forensics |

---

## Template Notes

Delete this section after completing the template.

**To complete this document:**

1. Replace all `[PLACEHOLDERS]` with your project specifics
2. Map your agents to the OWASP Agentic AI Top 10
3. Draw your trust boundary diagram
4. Define authentication flows for users and agents
5. Create authorization matrix for all agents
6. Define audit events relevant to your domain
7. Set up escalation rules for your context
8. Complete the security checklist

**Document security decisions:** Use [ADR_TEMPLATE.md](ADR_TEMPLATE.md) for significant security choices (auth approach, trust boundaries, escalation rules).

**OWASP Agentic AI Top 10 (2026) Reference:**

| ID | Risk Name | Description |
|----|-----------|-------------|
| ASI01 | Agent Goal Hijack | Manipulation of agent objectives |
| ASI02 | Tool Misuse | Exploitation of agent tools |
| ASI03 | Identity Abuse | Privilege escalation between agents |
| ASI04 | Supply Chain | Compromised dependencies |
| ASI05 | Code Execution | Unauthorized code execution |
| ASI06 | Memory Poisoning | Corruption of agent memory |
| ASI07 | Inter-Agent Attack | Communication spoofing |
| ASI08 | Cascading Failure | Chain reaction failures |
| ASI09 | Trust Exploitation | Abuse of human-agent trust |
| ASI10 | Rogue Agents | Agents acting outside governance |

---

## Validation Schema (For AI Generation)

```yaml
# SECURITY_ARCHITECTURE validation gate
inputs_required:
  - brand.license: from_brand_guidelines
  - northstar.personas[]: from_northstar
  - extract.axioms[]: from_northstar_extract
  - arch.agent_topology[]: from_architecture_blueprint

outputs_produced:
  - security.threat_model: used_by_operations
  - security.auth_patterns: used_by_implementation
  - security.trust_boundaries: used_by_implementation
  - security.audit_requirements: used_by_post_deployment

validation_gate:
  required_sections:
    - "Threat Model"
    - "Authentication & Authorization"
    - "Trust Boundaries"
    - "Audit & Incident Response"
    - "Security Checklist"

  minimum_content:
    threats: 5
    auth_pattern: specified
    trust_boundaries: defined
    prohibited_uses: 3
    escalation_rules: 2

  placeholders_filled:
    - "[PROJECT NAME]"
    - "[vector]"
    - "[VECTOR 1]"

cross_references:
  - prohibited_uses: must_align_with: brand.license.prohibited
  - trust_boundaries: must_cover: arch.agent_topology
  - escalation_rules: must_align_with: extract.axioms

quality_checks:
  - threat_model: covers_owasp_agentic_top_10
  - trust_boundaries: has_diagram
  - auth_flow: has_sequence_diagram
  - audit_events: have_severity_levels

decision_prompts:
  - "What authentication method? (API keys, OAuth, JWT, session-based)"
  - "Who can escalate to human review and when?"
  - "What data must never be logged?"
  - "What's your incident response SLA?"
```

**After generation, verify:**
- [ ] No `[PLACEHOLDER]` text remains
- [ ] Threat model covers at least 5 OWASP agentic threats
- [ ] Authentication pattern is fully specified
- [ ] Trust boundaries cover all agents in topology
- [ ] Prohibited uses align with BRAND_GUIDELINES license
- [ ] Escalation rules are clear and actionable

---

© [YEAR] [AUTHOR/ORG]

