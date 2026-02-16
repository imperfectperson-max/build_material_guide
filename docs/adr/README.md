# Architecture Decision Records (ADR)

**Building Materials Price Intelligence Platform**  
**ADR Index and Guidelines**

---

## 📋 Overview

This directory contains Architecture Decision Records (ADRs) for the Building Materials Price Intelligence Platform. ADRs document significant architectural decisions made during the project, including context, rationale, alternatives considered, and consequences.

**Purpose:** Preserve the reasoning behind architectural choices to help current and future team members understand why the system is designed the way it is.

---

## 📝 What is an ADR?

An Architecture Decision Record (ADR) is a document that captures an important architectural decision made along with its context and consequences.

### When to Create an ADR

Create an ADR when making decisions about:

- ✅ Technology stack selections (databases, frameworks, libraries)
- ✅ System architecture patterns (microservices, monolith, etc.)
- ✅ Infrastructure choices (hosting platforms, deployment strategies)
- ✅ Security approaches (authentication methods, encryption)
- ✅ Data storage and caching strategies
- ✅ Integration patterns and external dependencies
- ✅ Development practices and tooling

### When NOT to Create an ADR

Don't create ADRs for:

- ❌ Implementation details that don't affect architecture
- ❌ Routine bug fixes
- ❌ Minor refactoring
- ❌ UI/UX design decisions (unless they impact architecture)
- ❌ Temporary workarounds

---

## 📚 ADR List

### Active ADRs

| Number | Title | Status | Date | Author |
|--------|-------|--------|------|--------|
| [0001](./0001-use-postgresql-for-main-database.md) | Use PostgreSQL for Main Database | Accepted | 2026-03-01 | Backend Team |
| [0002](./0002-use-redis-for-caching.md) | Use Redis for Caching Layer | Accepted | 2026-03-01 | Backend Team |

### Superseded ADRs

| Number | Title | Status | Superseded By | Date |
|--------|-------|--------|---------------|------|
| None yet | - | - | - | - |

### Deprecated ADRs

| Number | Title | Status | Reason | Date |
|--------|-------|--------|--------|------|
| None yet | - | - | - | - |

---

## 🔄 ADR Lifecycle

### Statuses

- **Proposed**: The decision is suggested but not yet approved
- **Accepted**: The decision is approved and is or will be implemented
- **Deprecated**: The decision is no longer recommended (but may still be in use)
- **Superseded**: The decision has been replaced by a new decision (specify which ADR)

### Workflow

```
Proposed → Accepted → (Deprecated/Superseded)
    ↓
 Rejected
```

1. **Proposed**: Team member creates ADR with "Proposed" status
2. **Review**: Team discusses during architecture review meeting
3. **Decision**: Team votes to Accept or Reject
4. **Implementation**: If accepted, implementation proceeds
5. **Updates**: Status changes to Deprecated/Superseded when replaced

---

## 📖 How to Use This Directory

### Creating a New ADR

1. **Copy the Template**
   ```bash
   cp docs/adr/template.md docs/adr/NNNN-short-title.md
   ```

2. **Number the ADR**
   - Use the next sequential number (0001, 0002, 0003, etc.)
   - Zero-pad to 4 digits

3. **Write a Descriptive Title**
   - Use kebab-case
   - Be specific but concise
   - Example: `0003-use-jwt-for-authentication`

4. **Fill in the Template**
   - Provide context and problem statement
   - Explain the decision and rationale
   - List alternatives considered
   - Document consequences (good and bad)

5. **Submit for Review**
   - Create a pull request
   - Tag team members for review
   - Present in architecture meeting if significant

6. **Update the Index**
   - Add entry to the table above
   - Set initial status as "Proposed"

### Updating an Existing ADR

When circumstances change:

1. **Don't Edit Accepted ADRs**
   - ADRs are historical records
   - Create a new ADR that supersedes the old one

2. **Update Status Only**
   - Change status to "Deprecated" or "Superseded"
   - Add note pointing to new ADR

3. **Create New ADR**
   - Reference the old ADR in context
   - Explain what changed and why

---

## 🎯 ADR Best Practices

### Writing Guidelines

**DO:**
- ✅ Be concise but complete
- ✅ Use clear, simple language
- ✅ Provide concrete examples
- ✅ Quantify when possible (performance, cost, etc.)
- ✅ Include references and research
- ✅ Document both pros and cons
- ✅ Consider long-term implications

**DON'T:**
- ❌ Leave sections empty
- ❌ Use vague or ambiguous language
- ❌ Skip alternatives section
- ❌ Make decisions without team input
- ❌ Forget to update the index

### Review Criteria

When reviewing ADRs, consider:

1. **Completeness**: All sections filled out adequately
2. **Clarity**: Decision and rationale are clear
3. **Alternatives**: Multiple options were considered
4. **Consequences**: Impacts are well understood
5. **Feasibility**: Decision is realistic given constraints
6. **Alignment**: Fits with overall architecture and goals

---

## 📝 ADR Template Structure

See [template.md](./template.md) for the full template.

### Key Sections

1. **Title**: Short, descriptive name
2. **Status**: Proposed/Accepted/Deprecated/Superseded
3. **Context**: Background and problem statement
4. **Decision**: What was decided and why
5. **Consequences**: Positive and negative impacts
6. **Alternatives**: Other options considered

---

## 🔗 Related Documentation

- [System Architecture](../architecture/SYSTEM_ARCHITECTURE.md)
- [Technology Choices](../../TECHNOLOGY_CHOICES.md)
- [Security Documentation](../../SECURITY.md)

---

## 💡 Example ADRs

### Technology Decisions
- ADR-0001: Use PostgreSQL for Main Database
- ADR-0002: Use Redis for Caching Layer

### Architecture Patterns
- (Future) ADR-NNNN: Use JWT for Authentication
- (Future) ADR-NNNN: Implement Two-Tier Caching Strategy

### Infrastructure Decisions
- (Future) ADR-NNNN: Deploy Backend on Fly.io
- (Future) ADR-NNNN: Use Vercel for Frontend Hosting

---

## 📞 Questions?

For questions about ADRs:
- Review existing ADRs for examples
- See the template for guidance
- Ask in team Slack channel
- Discuss in architecture meetings

---

**Last Updated:** March 9, 2026  
**Total ADRs:** 2  
**Next Review:** Monthly
