# Security Policy & Incident Response Plan (Lemnos Labs)

**Effective Date:** October 2026  
**Vendor:** Lemnos Labs (`lemnoslabs@proton.me`)  
**Applies to:**
1. **Bulk User & License Hygiene for Jira Cloud** (`dev.lemnos.jira.bulk-user-hygiene`)
2. **Markdown & Folder Hierarchy Importer for Confluence** (`dev.lemnos.confluence.markdown-hierarchy-importer`)
3. **Markdown, Mermaid, LaTeX & BDD Spec Studio for Jira** (`dev.lemnos.jira.markdown-mermaid-bdd-studio`)

---

## 1. Architecture & Zero-Egress Posture (`Runs on Atlassian`)

All Lemnos Labs Marketplace applications are built **100% natively on the Atlassian Forge platform** (Node.js 22 isolated runtime):
- **Zero External Servers:** Lemnos Labs operates no external application servers, databases, or third-party cloud backends for customer data processing.
- **Zero External Data Egress:** Neither `manifest.yml` declares any `permissions.external` egress domains. All API calls execute via `api.asUser().requestJira(route`...`)` or `api.asUser().requestConfluence(route`...`)` within Atlassian's tenant boundary.
- **Encryption at Rest & in Transit:** All platform storage (`storage:app`) is encrypted at rest (AES-256) and in transit (TLS 1.2+) by the Atlassian Forge platform.
- **No Credential or PII Logging:** Application logs never output credentials, tokens, passwords, or personal data (`accountId` only where operationally required).

---

## 2. Vulnerability Management (SAST & SCA)

Before any production release (`forge deploy -e production`), our automated security verification (`e020/security_audit.py`) executes:
1. **SCA (Software Composition Analysis):** `npm audit --omit=dev` across all Forge packages (`0` known vulnerabilities permitted).
2. **SAST (Static Application Security Testing):** Automated static analysis checking for OWASP Top 10 risks:
   - Prohibition of dynamic code execution (`eval`, `new Function`, `setTimeout(string)`).
   - Prohibition of unescaped HTML/XHTML injection (`innerHTML` with unsanitized user input; all Confluence Storage XHTML output is strictly XML-escaped and CDATA-guarded).
   - Strict Forge `route` tagged template usage (`route`...``) to prevent path traversal and SSRF.
   - Zero hardcoded secrets, tokens, or API keys in source files or `manifest.yml`.

### Marketplace Security Bug Fix SLAs
In accordance with the [Atlassian Marketplace Security Bug Fix Policy](https://developer.atlassian.com/platform/marketplace/security-bugfix-policy/):
- **Critical Severity Vulnerabilities:** Patched and deployed to Forge `production` within **2 weeks** (maximum 4 weeks).
- **High Severity Vulnerabilities:** Patched and deployed to Forge `production` within **4 weeks**.
- **Medium / Low Severity Vulnerabilities:** Patched in the next scheduled maintenance release.

---

## 3. Security Incident Response Plan

In the event of a suspected or confirmed security incident or critical vulnerability affecting any Lemnos Labs app:

### Phase 1: Triage & Containment (0–4 Hours)
1. **Acknowledge & Isolate:** Security alerts reported via `lemnoslabs@proton.me`, GitHub Security Advisories, or Atlassian Marketplace Security (`ecosystem.atlassian.net` / AMS) are triaged immediately by the Primary Security Contact (`Gregor Mengelkamp`, `lemnoslabs@proton.me`).
2. **Token & Credential Rotation:** If any developer CLI token or repository key is suspected of compromise, all Atlassian API tokens and SSH deploy keys are revoked and rotated immediately.

### Phase 2: Notification to Atlassian & Customers (Within 24 Hours)
Following the [Atlassian Security Incident Management Guidelines](https://developer.atlassian.com/platform/marketplace/app-security-incident-management-guidelines/) and [App Vulnerability Notification Templates](https://developer.atlassian.com/platform/marketplace/app-vulnerability-notification-template/):
1. **Notify Atlassian Security:** Submit an incident report via the Atlassian Ecosystem Service Desk (`ecosystem.atlassian.net`) detailing root cause, affected versions, and blast radius.
2. **Notify Affected Customers:** Where customer action or awareness is required, issue a structured advisory using Atlassian's Vulnerability Notification Template.

### Phase 3: Remediation & Post-Mortem (Within 24–72 Hours)
1. Develop, verify (`node --test` + `security_audit.py`), and deploy the hotfix via `npx forge deploy -e production`.
2. Verify automatic rollout across Forge production environments and document a regression test preventing recurrence.
