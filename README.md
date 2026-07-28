# AI-Powered Email Assistant

UiPath RPA solution that monitors Outlook, extracts action items using
Google Gemini, and creates calendar events, Microsoft To Do tasks, and
draft replies automatically. Also files document attachments into a
categorised library with AI-generated summaries.

Built as a summer COOP internship project at Saudi Aramco, 2026.

## Setup
1. Copy `Config/Config.template.json` to `Config/Config.json`
2. Add your Gemini API key from https://aistudio.google.com
3. Open `project.json` in UiPath Studio 2026 (Windows compatibility)
4. Ensure classic Outlook desktop is running and signed in

## Requirements
- UiPath Studio 2026 Community Edition
- Classic Outlook desktop with a Microsoft Exchange account
- Microsoft Word and Excel (for Office attachment parsing)
- A Gemini API key (free tier)