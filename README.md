# n8n Competitor Monitoring Workflow

An automated n8n workflow that monitors competitor websites for changes and uses AI to analyze updates.

## Overview

This workflow:
- Runs daily at 6 AM
- Monitors 6 competitor websites (pricing and product pages)
- Detects content changes using SHA-256 hashing
- Uses OpenAI GPT-3.5 to analyze significant changes
- Logs everything to Google Sheets

## Features

- **Automated Monitoring**: Schedule-based competitor tracking
- **Change Detection**: Hash-based comparison to identify updates
- **AI Analysis**: GPT-3.5 powered change summarization
- **Error Handling**: Graceful failure management with error logging
- **Data Persistence**: Google Sheets integration for historical tracking

## Files

- `competitor-monitoring-refined.json` - Enhanced workflow with improvements
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
11. **Log Change** - Records to Changes sheet
12. **Update Main Sheet** - Updates hash and timestamp
13. Loop continues to next competitor

## Key Improvements Over Original

- ✅ Fixed loop-back logic (removed problematic "Go To Next" node)
- ✅ Added comprehensive error handling
- ✅ Enhanced AI prompts for better analysis
- ✅ Added error logging to separate sheet
- ✅ Improved data tracking (page_type, content_length, status)
- ✅ Better code comments and validation
- ✅ Consistent naming conventions

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

## Monitoring

- Check the **Errors** sheet regularly for failed fetches
- Review the **Changes** sheet for competitor updates
- The **Pages** sheet shows current state of all monitored pages

## License

MIT
