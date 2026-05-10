#  SDLC (Software Development Life Cycle)

## What is SDLC?

Standard process to:

- Plan
- Design
- Develop
- Test
- Deploy
- Maintain software

### Goal

```text
Deliver high-quality software.
```

---

## Why SDLC Matters

Without SDLC:

- Chaos
- Poor coordination
- Low quality
- Risky deployments

Used by:

- Startups
- MNCs
- Product companies
- Service companies

---

# SDLC Phases

## 1. Planning

Activities:

- Requirement gathering
- Customer feedback
- Business analysis
- Market research

Example:

```text
Amazon Fresh wants 15-minute delivery.
```

---

## 2. Analysis

Questions:

- Is it possible?
- Is it profitable?
- Do we have required skills?
- Should it be prioritized?

---

## 3. Design

### HLD (High-Level Design)

Focus:

- Architecture
- Scalability
- Availability
- Infrastructure
- Databases

Example:

```text
Application should handle high traffic.
```

### LLD (Low-Level Design)

Focus:

- Modules
- Functions
- APIs
- Implementation logic
- Arguments & responses

---

## 4. Build / Development

Developers:

- Write code
- Review code
- Push to Git repositories

Tool:

- Git

---

## 5. Testing

QA/QE Engineers:

- Test applications
- Validate functionality
- Detect bugs
- Ensure quality

---

## 6. Deployment

Application moves to:

- Production servers
- Customer-facing environment

---

## 7. Maintenance

Handled mainly by:

- SRE teams
- Support teams

Activities:

- Monitoring
- Alerting
- Reliability tracking
- Issue resolution

---

#  .....Organizational Roles.....

1. Customers

Most important entity.

Customers:

- Give feedback
- Request features
- Influence roadmap

Examples:

- 15-minute delivery
- UPI support

---

2. Business Analyst (BA)

Responsibilities:

- Customer interaction
- Requirement gathering
- Business understanding
- BRD preparation

### BRD

Business Requirement Document containing:

- Customer needs
- Feature requests
- Business goals

---

3. Product Manager (PM)

Responsibilities:

- Feature prioritization
- Product vision
- Competitor analysis
- Roadmap decisions

Example:

```text
15-minute delivery prioritized before payment gateway.
```

---

4. Product Owner (PO)

Responsibilities:

- Convert vision into work
- Create epics
- Define backlog
- Define acceptance criteria

Breaks work into:

- Epics
- Stories
- Tasks

---

5. Solution / Software Architect

Responsibilities:

- Prepare HLD & LLD
- Choose frameworks
- Design implementation
- Validate feasibility

---

6. Developers

Responsibilities:

- Implement features
- Write code
- Identify technical needs

May request:

- Kubernetes
- Docker
- Git repos
- Infrastructure

---

7. DevOps Engineers

### Responsibilities

- Workflow automation
- Infrastructure creation
- SDLC efficiency improvement
- Developer support
- Deployment/testing automation

Typical tasks:

- Kubernetes setup
- CI/CD setup
- Infrastructure provisioning
- Deployment automation
- Cloud setup
- Git integration

### Important Point

DevOps engineers usually:

```text
DO NOT directly receive customer requirements.
```

Requirements mostly come from:

- Developers
- Architects
- Scrum teams

---

8. QA / QE Engineers

Responsibilities:

- Testing
- Quality validation
- Bug detection

---

9. DBA :- Database Administrator 

Responsibilities:

- Database management
- Administration
- Optimization

---

10. SRE :- site Relibility engineer comesafter making a product live, they monitor uptime with dashboard and matrics and alerts for erros

Responsibilities:

- Reliability
- Uptime monitoring
- Dashboards
- Metrics
- Alerts

---

11. Technical Writers 

Responsibilities:

- Documentation for customers and developers
- Feature explanation
- Technical docs maintenance

---

# 4. Scrum Team

May include:

```text
Developers
DevOps Engineers
QA Engineers
DBA
Technical Writers
```

### Goal

```text
Collaboratively complete requirements.
```

---

# 5. Agile, Scrum & Sprint

## Agile

Means:

- Iterative development
- Smaller delivery cycles
- Faster feedback
- Continuous improvement

---

## Scrum

Framework used inside Agile.

Works with:

- Sprints
- Backlogs

---

## Sprint

```text
2–3 weeks of planned work.
```

During sprint:

- Tasks assigned
- Progress tracked
- Work completed

---

## Sprint Planning

Team decides:

- Work to take
- Completion feasibility
- Ownership

---

## Sprint Retrospective

Meeting after sprint.

Purpose:

- Review work
- Identify blockers
- Improve next sprint

---

# 6. Jira Concepts

## Jira

Project management & tracking tool.

Used for:

- Task tracking
- Visibility
- Collaboration
- Accountability

---

## Epic

Large business feature.

Example:

```text
15-minute delivery service
```

---

## Story

Small actionable task.

Examples:

- Create Kubernetes cluster
- Setup AWS RDS
- Create mobile UI

---

## Backlog

Storage for pending work.

Items stay until selected for sprint.

---

## Workflow

```text
Epic
↓
Stories
↓
Backlog
↓
Sprint
↓
In Progress
↓
Review
↓
Done
```

---

# Requirement Flow

```text
Customer
↓
Business Analyst
↓
Product Manager
↓
Product Owner
↓
Solution Architect
↓
Scrum Team
↓
DevOps Engineer receives infra/automation tasks
```

---

# Example DevOps Flow

Requirement:

```text
15-minute grocery delivery
```

Developer identifies:

```text
Need Kubernetes cluster
```

Story created:

```text
Create Kubernetes Cluster
```

Assigned to:

```text
DevOps Engineer
```

---

# 7. DevOps Role in SDLC

Main focus:

- Build automation 
- Testing automation ( ex :- writing ci/cd for running testing scripts to test etc etc)
- Deployment automation
- Efficiency improvement

---

## DevOps Goal

Reduce:

- Manual work
- Delays
- Human errors

Increase:

- Speed
- Reliability
- Automation
- Consistency

---

## Automation Example

### Without DevOps

```text
Developer manually builds
Tester manually tests
Ops manually deploys
```

Problems:

- Slow delivery
- Human mistakes
- Inconsistent deployments

---

### With DevOps

```text
Code Push
↓
Automated Build
↓
Automated Testing
↓
Automated Deployment
```

---

# CI/CD (High Level)

CI/CD helps:

- Automate testing
- Automate deployment
- Reduce manual intervention

---

# 8. Important Interview Questions

## What is SDLC?

Standard process to:

- Plan
- Design
- Develop
- Test
- Deploy software

---

## Important SDLC Phases for DevOps

Mainly:

- Build
- Testing
- Deployment

Reason:

```text
DevOps engineers automate these phases.
```

---

## HLD vs LLD

### HLD

- Overall architecture
- Scalability
- Infrastructure decisions

### LLD

- Functions
- APIs
- Module-level logic

---

## Epic vs Story

### Epic

Large business feature

### Story

Small actionable task

---

## What does Jira solve?

Provides:

- Visibility
- Tracking
- Accountability
- Collaboration

---

# 9. Common Beginner Mistakes

## Mistake 1

Thinking DevOps = tools only.

Reality:

- Culture
- Collaboration
- Automation
- Efficiency

---

## Mistake 2

Thinking DevOps engineers directly receive customer requirements.

Usually false.

---

## Mistake 3

Confusing:

- Epic
- Story
- Sprint
- Backlog

---

## Mistake 4

Memorizing SDLC phases without understanding WHY they exist.

---

# 10. Revision Mindmap

```text
Customer
↓
Business Analyst
↓
Product Manager
↓
Product Owner
↓
Solution Architect
↓
Scrum Team
    ├── Developers
    ├── DevOps Engineers
    ├── QA Engineers
    ├── DBA
    └── Technical Writers
↓
Build
↓
Test
↓
Deploy
↓
Monitor
```

---

# 11. Final Core Understanding

```text
DevOps engineers improve software delivery efficiency using automation and collaboration.
```
