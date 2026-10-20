# Privacy & Data Security Policy

**Last Updated:** October 2026  
**Scope:** Atlassian Forge Apps — **Bulk User & License Hygiene for Jira Cloud** (`k1-bulk-user-admin`) & **Markdown & Folder Hierarchy Importer for Confluence** (`k2-markdown-importer`)

---

## 1. Zero External Data Egress ("Runs on Atlassian")

Both applications are built exclusively on the **Atlassian Forge** platform and operate under a strict **Zero External Data Egress** architecture:

- **No External Servers:** Neither application transmits, stores, or proxies any user data, directory metadata, email addresses, or Confluence page contents to any external third-party server, SaaS endpoint, or analytics service.
- **No External AI / LLM APIs at Runtime:** All auditing, CSV parsing, FNV-1a content hashing, and Markdown-to-Confluence Storage XHTML compilation execute 100% deterministically inside your browser's Custom UI sandbox and Atlassian's hosted Forge runtime.
- **In-Memory Processing Only:** User lists, group memberships, CSV activity exports, and Markdown files are processed transiently in memory during your active session to execute the Jira or Confluence REST API calls you explicitly authorize.

---

## 2. Data Accessed via Atlassian Platform APIs

### A. Bulk User & License Hygiene for Jira Cloud
- **Scopes Used:** `read:jira-user`, `manage:jira-configuration`
- **Purpose:** Reads Jira Cloud directory users and group memberships to compute active vs. dormant billable seat counts, enforce safety guardrails (`org-admins`, `site-admins`, `app` bot accounts), and execute admin-approved batch group removals or user deactivations after a mandatory Pre-Flight Dry-Run.

### B. Markdown & Folder Hierarchy Importer for Confluence
- **Scopes Used:** `read:page:confluence`, `write:page:confluence`, `read:space:confluence`
- **Purpose:** Reads existing page titles, versions, and Storage XHTML in the target Confluence Space to compute 32-bit FNV-1a content hashes (`SKIP_UNCHANGED`), and creates or updates Confluence pages in level-order waves (`L0 -> LD`) when initiated by the user.

---

## 3. Data Retention & Deletion

Because neither application persists customer data outside your Atlassian Cloud instance:
- Uninstalling the app immediately terminates all access.
- There is no external database from which customer data needs to be purged.

---

## 4. Contact & Security Inquiries

For privacy or security questions, please open an issue on our public support tracker or contact support as listed in [SUPPORT.md](./SUPPORT.md).
