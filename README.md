# n8n Competitor Monitoring Workflow

An automated n8n workflow that monitors competitor websites for changes and uses AI to analyze updates.

## Overview

This workflow:
- Runs daily at 6 AM
- Monitors 6 competitor websites (pricing and product pages)
- Detects content changes using SHA-256 hashing
- Uses OpenAI GPT-3.5 to analyze significant changes
- Logs everything to Google Sheets
- **Saves competitor change notes directly to your Obsidian vault**

## Features

- **Automated Monitoring**: Schedule-based competitor tracking
- **Change Detection**: Hash-based comparison to identify updates
- **AI Analysis**: GPT-3.5 powered change summarization
- **Error Handling**: Graceful failure management with error logging
- **Data Persistence**: Google Sheets integration for historical tracking
- **Obsidian Integration**: Automatically creates/appends structured Markdown notes in your vault

## Files

- `competitor-monitoring-refined.json` - Enhanced workflow with Obsidian integration
- `IMPROVEMENTS.md` - Detailed documentation of all enhancements

## Competitors Monitored

1. **Awardco** - Pricing page
2. **O.C. Tanner** - Product page
3. **Achievers** - Product page
4. **Bonusly** - Pricing page
5. **Motivosity** - Pricing page
6. **Nectar** - Pricing page

## Setup

### Prerequisites

1. n8n instance (self-hosted or cloud)
2. Google Sheets API credentials
3. OpenAI API key
4. Obsidian with the **Local REST API** community plugin installed and enabled

### Obsidian Setup

1. Open Obsidian → **Settings → Community Plugins**
2. Search for **"Local REST API"** by coddingtonbear and install it
3. Enable the plugin and open its settings
4. Copy the **API Key** shown in the plugin settings
5. Note the port (default: `27123`) — the n8n workflow calls `http://localhost:27123`
6. In your vault, create a folder called `Competitor Intelligence` (the workflow will create notes inside it automatically)

> **Note:** The Local REST API plugin only accepts connections from localhost. If your n8n instance runs on a separate machine, you will need to expose the API via a reverse proxy with appropriate authentication.

### Obsidian Credential in n8n

1. In n8n, go to **Credentials → New**
2. Choose **Header Auth**
3. Set **Name** to `Obsidian Local REST API`
4. Set **Header Name** to `Authorization`
5. Set **Header Value** to `Bearer <your-api-key>` (replace with the key from the plugin)
6. In the `Send to Obsidian` node, update the credential ID to match the one you just created

### Google Sheets Structure

Create a spreadsheet with three sheets:

**Pages** (main tracking):
```
url | competitor | page_type | hash | last_seen | content_length | status
```

**Changes** (change log):
```
timestamp | competitor | url | page_type | change_summary | content_length
```

**Errors** (error tracking):
```
timestamp | competitor | url | error | status
```

### Installation

1. Import `competitor-monitoring-refined.json` into n8n
2. Update Google Sheets document ID in all Google Sheets nodes
3. Configure credentials:
   - Google Sheets OAuth2
   - OpenAI API
   - Obsidian Local REST API (Header Auth — see above)
4. Activate the workflow

## How It Works

1. **Schedule Trigger** - Runs at 6 AM daily
2. **Define Targets** - Lists all competitor URLs
3. **Format List** - Converts to processable format
4. **Loop Through Competitors** - Iterates through each target
5. **Fetch Page** - Downloads webpage content
6. **Check Success** - Validates fetch succeeded
7. **Clean & Hash** - Removes HTML tags and creates content hash
8. **Lookup Existing** - Checks for previous hash in database
9. **Content Changed?** - Compares hashes
10. **Analyze Change** (if changed) - AI analyzes the update
11. **Log Change** - Records to Google Sheets Changes tab
12. **Format Obsidian Note** - Builds a structured Markdown entry
13. **Send to Obsidian** - Appends the entry to `Competitor Intelligence/{CompetitorName}.md` in your vault
14. **Update Main Sheet** - Updates hash and timestamp
15. Loop continues to next competitor

### Obsidian Note Format

Each change creates an entry like this inside your vault:

```markdown
---

## May 8, 2026 at 06:14 AM

**Page Type:** pricing
**URL:** https://www.awardco.com/plans
**Content Length:** 42183 chars

### Change Summary

Awardco updated their pricing page to introduce a new Enterprise tier with
custom seat pricing. The "Teams" plan price increased from $3 to $4 per user
per month. A new annual billing discount of 20% was added to all plans.
```

## Key Improvements Over Original

- ✅ Fixed loop-back logic (removed problematic "Go To Next" node)
- ✅ Added comprehensive error handling
- ✅ Enhanced AI prompts for better analysis
- ✅ Added error logging to separate sheet
- ✅ Improved data tracking (page_type, content_length, status)
- ✅ Better code comments and validation
- ✅ Consistent naming conventions
- ✅ **Obsidian integration — competitor changes saved as Markdown notes**

## Customization

### Add More Competitors

Edit the `Define Targets` node and add to the `targets` array:

```json
{
  "competitor": "Company Name",
  "url": "https://example.com/page",
  "page_type": "pricing"
}
```

### Change Schedule

Modify the `Schedule Trigger` node's `triggerAtHour` value (0-23).

### Adjust AI Analysis

Edit the system prompt in the `Analyze Change` node to focus on different aspects.

### Change Obsidian Vault Folder

Edit the `Format Obsidian Note` node and change `Competitor Intelligence` in the `notePath` line to any folder name you prefer.

## Monitoring

- Check the **Errors** sheet regularly for failed fetches
- Review the **Changes** sheet for competitor updates
- The **Pages** sheet shows current state of all monitored pages
- Browse `Competitor Intelligence/` in Obsidian for a searchable history of all changes

## License

MIT
