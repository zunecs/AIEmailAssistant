# AI-Powered Email Assistant

A UiPath RPA solution that monitors Outlook, extracts action items using Google Gemini,
and automatically creates calendar events, Microsoft To Do tasks, draft replies, and a
categorised document library with AI-generated summaries.

Built as a summer COOP internship project, 2026.

---

## What it does

When a new email arrives, the robot reads it, sends the body and any attachments to a
large language model, and turns the structured response into real artefacts inside
Microsoft 365 — without sending, deleting, or transmitting anything outside the user's
own mailbox.

| Capability | Description |
|---|---|
| **Continuous monitoring** | Polls a configured Outlook folder and processes new mail automatically |
| **AI extraction** | Schema-constrained JSON response — no parsing guesswork |
| **Calendar events** | Outlook appointments with location and configurable reminders |
| **Prioritised tasks** | Outlook tasks that sync to Microsoft To Do, colour-coded by priority |
| **Draft replies** | Contextual replies saved to Drafts — never sent automatically |
| **Attachment intelligence** | Text extraction from PDF, Word, Excel, and plain-text files |
| **Document library** | Classifies, renames, and files documents with generated summaries |
| **Folder intake** | Applies the full pipeline to documents arriving outside email |
| **Duplicate suppression** | Semantic embeddings recognise restated tasks and skip them |
| **Bilingual** | Processes Arabic and English, replying in the original language |
| **Audit logging** | Structured CSV record of every processed item |
| **Weekly reporting** | Formatted activity report with an AI-written narrative, emailed on schedule |

---

## Architecture

Two input paths — Outlook and a watched folder — converge on a shared extraction and
output pipeline. Everything runs locally; the only external dependency is the Gemini API.

Outlook is driven through **COM automation** rather than Microsoft Graph, so the robot
inherits the signed-in user's identity with no API registration, no stored credentials,
and no administrative approval. Tasks created against an Exchange mailbox propagate to
Microsoft To Do automatically.

Eleven single-purpose workflows, each independently testable:

| Workflow | Responsibility |
|---|---|
| `Main.xaml` | Polling loop, error boundaries, report scheduling |
| `InitAllSettings` | Configuration loading and environment-variable expansion |
| `GetNewEmails` | Mailbox reader |
| `ExtractAttachments` | Attachment text extraction by file type |
| `CallGemini` | Extraction API client with retry and prompt guards |
| `CallEmbedding` | Vector generation for duplicate detection |
| `CreateOutlookItems` | Tasks and appointments |
| `CreateReplyDraft` | Reply composition |
| `FileToLibrary` | Document classification and filing |
| `ProcessIntakeFolder` | Watched-folder pipeline |
| `WriteAuditLog` | Structured logging |
| `GenerateWeeklyReport` | Aggregation and reporting |

---

## Requirements

- Windows with **UiPath Studio 2026** Community Edition
- **Classic Outlook desktop**, signed in to a Microsoft Exchange account and running
- **Microsoft Word and Excel** (for Office attachment extraction)
- A **Gemini API key** — free tier, from [Google AI Studio](https://aistudio.google.com)

> The new Outlook for Windows removes COM support and is not compatible.

---

## Setup

1. Clone the repository and open `project.json` in UiPath Studio.
2. Copy `Config/Config.template.json` to `Config/Config.json`.
3. Add your Gemini API key and report recipient address.
4. Adjust the monitored folder, poll interval, and library paths as needed.
5. In Outlook, create three categories named `AIEA-High`, `AIEA-Normal`, `AIEA-Low`,
   plus the processed-mail folder named in your configuration.
6. Run from Studio to verify, then publish and run from UiPath Assistant.

`Config/Config.json` is git-ignored — it holds your API key and is never committed.

---

## Configuration

All behaviour is controlled by a single JSON file; no workflow changes are required to
retune the system. Thirty-three settings cover:

- **Mailbox and scheduling** — folder, poll interval, batch caps, timezone
- **Language model** — endpoint, models, prompt ceiling, retry policy
- **Extraction behaviour** — meeting duration, reminder lead time, similarity threshold
- **Documents and reporting** — supported types, library paths, report schedule

Paths may contain Windows environment variables (`%USERPROFILE%`), expanded at load time
so the project stays portable across machines.

---

## Documentation

Full technical documentation — architecture, design decisions, validation results, and
known limitations:

**[`Documentation/AI_Email_Assistant_Technical_Documentation.docx`](Documentation/)**

The design decisions section covers the reasoning behind the significant choices,
including why Microsoft Graph was rejected, why the standard UiPath enterprise framework
was not used, and why lexical duplicate detection was replaced with semantic embeddings.

---

## Project structure

AIEmailAssistant/
├── Main.xaml Orchestrating polling loop
├── Config/ Settings, prompts, request templates
├── Workflows/ Eleven single-purpose workflows
└── Documentation/ Technical documentation

Runtime artefacts — the audit log, vector cache, temporary attachments, and the document
library — are excluded from version control. They contain real sender addresses and
document content.

---

## Known limitations

- **Windows-only.** COM automation requires classic Outlook installed and running.
- **Attended operation.** Unattended scheduling requires a licensed orchestration tier.
- **Duplicate cache reflects creation, not existence.** A manually deleted task will not
  be recreated until its cache entry ages out.
- **Replies are not threaded.** The mail object does not expose the identifier needed for
  a true threaded reply; drafts are composed as new messages.
- **Scanned PDFs are not read.** Image-based documents yield no text.

Full list in the technical documentation.

---

## Notes

All company names, personal names, and document contents in the demonstration material
are fictional and were created for testing purposes.