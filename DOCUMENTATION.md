# Administrator & User Documentation

Complete setup and operational guide for our zero-egress Atlassian Forge tools.

---

## Part 1: Bulk User & License Hygiene for Jira Cloud (`k1-bulk-user-admin`)

### Overview
**Bulk User & License Hygiene** helps Jira Cloud Administrators audit dormant billable seats, preview exact monthly and annual license savings, verify safety guardrails, and reclaim wasted licenses in batches—without ever sending user directory data outside Atlassian Forge.

### Key Features
1. **Two-Pass Seat & Waste Audit:** Scans your directory (or a pasted Atlassian Organization CSV export) in milliseconds and categorizes accounts into Active Billable Seats, Dormant Buckets (`30d+`, `60d+`, `90d+`, `180d+`, `Never Active`), and Guarded Accounts (`org-admins`, `app` bots).
2. **RFC 4180 CSV Last-Active Import:** Paste your Atlassian Admin `export-users.csv` (`accountId,email,lastActiveDate`) directly into the CSV drawer to enrich Jira Cloud users with exact last-active timestamps.
3. **Mandatory Two-Step Pre-Flight Dry-Run:**
   - **Step 1 (`1. Run Pre-Flight Dry-Run`):** Emits **zero** HTTP mutations and displays a side-by-side verification report listing every account that **Will Execute** vs. every account **Blocked by Safety Guardrails** (`PROTECTED_ADMIN_GROUP`, `APP_BOT_ACCOUNT`, `RECENTLY_ACTIVE_USER`, `OPERATOR_SELF_ACCOUNT`).
   - **Step 2 (`2. Execute Verified Batch`):** Unlocks only after Dry-Run verification passes with at least 1 eligible account.
4. **1-Click Demo Sandbox:** Includes a built-in 10-account corporate demo dataset (`Reset Demo Data (10)`) so administrators and reviewers can test every filter and guardrail on a fresh test instance before syncing live users (`Sync Live Jira Directory`).

### Quickstart Workflow
1. Open **Jira Settings → Apps → Bulk User & License Hygiene**.
2. Click **Sync Live Jira Directory** (or test with the default 10-user Demo Directory). Optionally open the **CSV / Clipboard Bulk Match** drawer and paste your Atlassian Org CSV export.
3. Set your **Seat Price ($/mo)** and **Inactivity Threshold** (recommended: `Dormant ≥ 90 days`).
4. Choose your batch action (`Remove from License Group(s)` is recommended and reversible).
5. Click **`1. Run Pre-Flight Dry-Run`**, inspect the guardrail report, and click **`2. Execute Verified Batch`**.

---

## Part 2: Markdown & Folder Hierarchy Importer for Confluence (`k2-markdown-importer`)

### Overview
Existing Markdown importers on the Atlassian Marketplace flatten nested directory structures into a single chaotic list or overwrite unchanged pages on every re-run. **Markdown & Folder Hierarchy Importer** compiles nested Markdown folders into a level-order topological tree (`L0 -> L1 -> ... -> LD`), automatically synthesizes missing parent folder index pages, resolves relative cross-folder `.md` links into native Confluence `<ri:page>` links, and skips unchanged pages using 32-bit FNV-1a content hashes.

### Key Features
1. **Preserves Full Nested Folder Hierarchy (`webkitdirectory`):** Select an entire local documentation folder or multiple `.md`/`.txt` files. Missing intermediate folders automatically receive clean parent index pages.
2. **Two-Pass Cross-Folder Link & GFM Compiler:**
   - Converts GitHub-Flavored Markdown (GFM) tables, fenced code blocks (`<ac:structured-macro ac:name="code">`), and GitHub Alerts (`> [!NOTE]`, `> [!TIP]`, `> [!WARNING]`, `> [!IMPORTANT]`) into valid Confluence Storage XHTML.
   - Rewrites relative links (`../architecture/storage-engine.md`) into native Confluence `<ac:link><ri:page ri:content-title="..."/></ac:link>` references.
3. **32-bit FNV-1a Zero-Waste Incremental Sync:**
   - Computes a deterministic 32-bit FNV-1a hash of each compiled page (`title + storageXhtml`).
   - On subsequent imports (`Sync Live Space Hashes`), unchanged pages are marked `SKIP_UNCHANGED` (`0` HTTP API calls), while modified pages are marked `UPDATE (vN+1)`.
4. **Live Storage XHTML Inspector:** Click any row in the topological tree table to inspect the source Markdown alongside its compiled Confluence Storage XHTML payload before importing.

### Quickstart Workflow
1. Open **Confluence → Space Settings → Integrations → Markdown & Folder Hierarchy Importer**.
2. Enter your **Target Space ID** (and optional **Root Parent Page ID**).
3. Click **`Select Folder (webkitdirectory)`** to load your local `.md` tree (or click **`Load Demo Handbook (4 Files)`** to test with the built-in 4-file nested engineering handbook).
4. Click **`Sync Live Space Hashes`** and **`1. Preview Hierarchy & Diff`** to review `CREATE`, `UPDATE`, and `SKIP_UNCHANGED` badges and inspect compiled XHTML.
5. Click **`2. Import Tree Level-by-Level`** to publish all waves in topological order (`L0 -> LD`).
