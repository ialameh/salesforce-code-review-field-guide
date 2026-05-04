---
hide:
  - navigation
---

# Salesforce Code Review Field Guide

A production-grade, multi-lens methodology for reviewing Apex, LWC, and Salesforce configurations before deployment.

A code review is not a personal attack on the developer. It is an assessment of the code.

## Who this guide is for

This guide is for senior Salesforce developers, architects, and technical leads who review Apex, LWC, triggers, flows, and configuration before those changes reach production. It is also for teams that want to establish a consistent, high-signal review process across their org.

The audience has working knowledge of Salesforce development. This is not an introductory text. It assumes you know what a trigger is, what a governor limit is, and how a queueable differs from a batch. The guide focuses on how to review that code systematically, with evidence, and without missing the problems that cause production failures.

## What you get

The guide teaches a 13-lens review framework. Each lens is a specialist perspective that catches a different class of problems. Running all 13 lenses against any codebase produces a review that is deeper and more reliable than any single-pass review.

The 13 lenses are:

| Lens | What it catches |
|---|---|
| Architecture | God classes, mixed responsibilities, tight coupling |
| Governor Limits | SOQL/DML/callouts in loops, unbounded memory growth |
| SOQL and LDV | Non-selective queries, full-table scans, missing limits |
| Security and Authorization | Missing CRUD/FLS, unsafe AuraEnabled, missing permission gates |
| Bulkification | Single-record assumptions, per-record DML/queries |
| Async and Transaction | Queueable limits, batch chains, retry gaps, race conditions |
| Metadata-Driven Configuration | Config-code mismatches, half-implemented fields |
| LWC and Frontend Contract | Wire misuse, tracked object mutations, client-only security |
| Integration and Callout | Hardcoded endpoints, missing retry, token handling |
| Logging and Observability | Swallowed exceptions, missing context, sensitive data in logs |
| Test Quality | Coverage bait, fake assertions, missing negative/bulk tests |
| CI/CD and Packaging | Org-specific references, missing destructiveChanges |
| Code Quality | Dead code, magic numbers, public/global overexposure |

## The 9-stage review pipeline

Lenses are not used one at a time. The full review runs through 9 stages:

1. **Intake and Inventory** — Classify what exists.
2. **System Map** — Build the runtime flow.
3. **Evidence Extraction** — Ground every finding in code facts.
4. **Specialist Lens Reviews** — Run all 13 lenses independently.
5. **Failure Simulation** — Test scenarios: empty, large, concurrent, partial failure.
6. **Cross-Lens Contradiction Check** — Reconcile conflicting findings.
7. **Risk Ranking** — Prioritize by production impact.
8. **Final Report Generation** — Produce the 12-section report.
9. **Reviewer Self-Check** — Catch your own blind spots.

## Chapter map

```md
Quickstart
  Q1. 20-Minute Production Readiness Review
  Q2. Review Environment Setup

Foundations
  F1. The Multi-Lens Framework
  F2. Evidence and Certainty Language
  F3. Severity and Risk Ranking

Operations
  O1. Intake and Inventory
  O2. System Map
  O3. Failure Simulation
  O4. Cross-Lens Contradiction Check
  O5. Reviewer Self-Check

Design and Engineering (one chapter per lens)
  DE1.  Architecture Lens
  DE2.  Governor Limits Lens
  DE3.  SOQL and LDV Lens
  DE4.  Security and Authorization Lens
  DE5.  Bulkification Lens
  DE6.  Async and Transaction Lens
  DE7.  Metadata-Driven Configuration Lens
  DE8.  LWC and Frontend Contract Lens
  DE9.  Integration and Callout Lens
  DE10. Logging and Observability Lens
  DE11. Test Quality Lens
  DE12. CI/CD and Packaging Lens
  DE13. Code Quality Lens

Reference
  R1. Final Report Format
  R2. Lens Finding Templates
  R3. Patterns to Prefer

Worked Material
  WM1. Annotated Review Report
  WM2. Finding Examples by Lens
```

## 30-second version

- Every finding names the file, class, method, and line.
- Every finding uses a certainty label: Confirmed, Likely, Possible, Cannot Verify.
- Severity is production-impact driven, not finding-count driven.
- A good review makes the developer say: "I did not notice that, but it is true."
- Style issues are low severity unless they hide a real defect.
- Never treat LWC validation as authorization. Server-side checks are mandatory.

## Status

Active development. Core chapters are complete.

## License

CC BY 4.0