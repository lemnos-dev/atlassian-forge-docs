# Privacy & Data Security Policy

**Last Updated:** October 2026  
**Scope:** Atlassian Forge Apps — **Bulk User & License Hygiene for Jira Cloud** (`k1-bulk-user-admin`), **Markdown & Folder Hierarchy Importer for Confluence** (`k2-markdown-importer`), and **Markdown, Mermaid, LaTeX & BDD Spec Studio for Jira** (`k6-markdown-mermaid-field`)

---

## 1. Zero External Data Egress ("Runs on Atlassian")

All Lemnos Labs applications are built exclusively on the **Atlassian Forge** platform and operate under a strict **Zero External Data Egress** architecture:

- **No External Servers:** None of our applications transmit, store, or proxy any user data, directory metadata, email addresses, Jira issue specifications, or Confluence page contents to any external third-party server, SaaS endpoint, external diagram renderer (`mermaid.ink`), or analytics service.
- **No External AI / LLM APIs at Runtime:** All auditing, CSV parsing, FNV-1a content hashing, Gherkin BDD parsing, inline Mermaid SVG generation, LaTeX math formatting, and Markdown-to-Confluence Storage XHTML compilation execute 100% deterministically inside your browser's Custom UI sandbox and Atlassian's hosted Forge runtime.
- **In-Memory & Native Tenant Storage Only:** User lists, group memberships, CSV activity exports, Markdown files, and Jira issue specifications (`k6_spec_v1` Jira Issue Properties) are processed within your Atlassian Cloud tenant boundary.

---

## 2. Data Accessed via Atlassian Platform APIs

### A. Bulk User & License Hygiene for Jira Cloud
- **Scopes Used:** `read:jira-user`, `manage:jira-configuration`
- **Purpose:** Reads Jira Cloud directory users and group memberships to compute active vs. dormant billable seat counts, enforce safety guardrails (`org-admins`, `site-admins`, `app` bot accounts), and execute admin-approved batch group removals or user deactivations after a mandatory Pre-Flight Dry-Run.

### B. Markdown & Folder Hierarchy Importer for Confluence
- **Scopes Used:** `read:page:confluence`, `write:page:confluence`, `read:space:confluence`
- **Purpose:** Reads existing page titles, versions, and Storage XHTML in the target Confluence Space to compute 32-bit FNV-1a content hashes (`SKIP_UNCHANGED`), and creates or updates Confluence pages in level-order waves (`L0 -> LD`) when initiated by the user.

### C. Markdown, Mermaid, LaTeX & BDD Spec Studio for Jira
- **Scopes Used:** `read:jira-work`, `write:jira-work`, `storage:app`
- **Purpose:** Reads Jira project issues and existing `k6_spec_v1` Jira Issue Properties (`read:jira-work`) to compute 32-bit FNV-1a content hashes (`SKIP_UNCHANGED`), writes updated Markdown/Mermaid/LaTeX/Gherkin specification payloads to Jira Issue Properties (`write:jira-work`) after Pre-Flight Dry-Run and 32KB guardrail verification, and stores non-PII sync audit telemetry in encrypted Atlassian Forge storage (`storage:app`).

---

## 3. Data Retention & Deletion

Because neither application persists customer data outside your Atlassian Cloud instance:
- Uninstalling the app immediately terminates all access.
- There is no external database from which customer data needs to be purged.

---

## 4. Contact & Security Inquiries

For privacy or security questions, please open an issue on our public support tracker or contact support as listed in [SUPPORT.md](./SUPPORT.md).
