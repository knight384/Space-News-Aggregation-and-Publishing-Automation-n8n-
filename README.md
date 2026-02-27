# Space-News-Aggregation-and-Publishing-Automation-n8n-

Overview

This workflow is a fully automated AI-powered space news aggregation system built in n8n.

It:

Collects space-related news every 15 minutes

Deduplicates articles using hashing + similarity logic

Extracts structured metadata using AI

Calculates a credibility score (0–100)

Generates short + long summaries using GPT-4

Publishes to Slack and CMS

Stores raw & processed data

Sends a daily HTML digest email at 06:00 UTC

Logs and handles errors gracefully

🏗️ Architecture Overview
1️⃣ Automated News Collection (Every 15 Minutes)

Triggered by:

Schedule Trigger (15 min interval)

Sources:

RSS Feed (NASA / ESA / Space.com)

Twitter Search (Space keywords)

Custom HTTP API

All sources are merged into one unified pipeline.

2️⃣ Normalization & Deduplication
✅ Normalization

Fields unified:

title

source_url

published_at

raw_content

source_type

✅ URL Hashing

Crypto node generates:

url_hash
✅ Deduplication Logic

Custom Code Node:

Checks URL hash against previously stored hashes

Filters items older than 2 hours

Uses Levenshtein distance (80% similarity threshold)

Prevents duplicate headlines

Seen hashes are stored in:

seen_urls_table
3️⃣ AI Metadata Extraction

Uses:

OpenAI GPT-4 (low temperature for accuracy)

Structured Output Parser (JSON schema)

Extracted fields:

title

summary

full_text

author

published_at

source_url

tags

detected_entities

missions

spacecraft

people

agencies

4️⃣ Credibility Scoring (0–100)

Scoring Criteria:

Condition	Points
Official domain (.gov, nasa.gov, esa.int, etc.)	+40
Cross-source confirmation (within 2h)	+30
Primary source link detected	+30

Publishing threshold:

credibility_score >= 60
5️⃣ AI Summary Generation

Uses GPT-4 to generate:

🔹 Slack Summary

50–75 words

🔹 CMS Summary

250–350 words

Output:

{
  "short_summary": "...",
  "long_summary": "..."
}
6️⃣ Publishing
📢 Slack

Posts:

Short summary

Source link

Credibility score

📰 CMS

HTTP POST with:

Title

Long summary

Tags

Entities

Credibility score

7️⃣ Data Storage
Raw Articles Table

Stores:

url_hash

source_url

title

raw_content

published_at

collected_at

Processed Articles Table

Stores:

title

summary

full_text

tags

entities

credibility_score

short_summary

long_summary

processed_at

8️⃣ Daily Email Digest (06:00 UTC)

Triggered daily.

Process:

Fetch processed articles from last 24 hours

Aggregate results

Format styled HTML email

Send via SMTP

Email contains:

Article list

Long summaries

Credibility score badge (color-coded)

Direct source links

🛡️ Error Handling

Implemented for:

Custom API

Slack publishing

CMS publishing

Email sending

Errors are logged with:

error_source

error_message

timestamp

related article info

HTTP nodes configured with:

Retry

Continue on error
