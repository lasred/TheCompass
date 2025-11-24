## Purpose
Create and launch email campaigns in Instantly with written emails, A/B testing, follow-up sequences, and proper sending configuration.

**CRITICAL:** Use Instantly V2 API only. API docs: https://developer.instantly.ai/api/v2

## Trigger
- After email approval
- "Launch campaigns"
- User types "LAUNCH" then "CONFIRM"

## Input
```json
{
  "niche": "HVAC inspection",
  "problem_angle": "JobCosting",
  "emails": [...],
  "inboxes": ["inbox1@domain1.com", "inbox2@domain1.com"],
  "path": "premium" | "budget"
}
```

## Campaign Configuration

### Naming
```
{Niche}_{Month}{Year}_{ProblemAngle}
```

Examples:
- `HVAC_Nov2025_JobCosting`
- `Fire_Nov2025_AESForms`
- `PropMgmt_Nov2025_ComplianceTracking`

### Sending Limits
- **Daily:** 25 emails per inbox
- **Per campaign:** 50 emails/day (2 inboxes)
- **All campaigns:** 150 emails/day total
- **Timeline:** ~600 emails ÷ 150/day = 4-5 days

### Settings
- Stop on reply: YES
- Stop on out-of-office: YES
- Track opens: YES
- Track clicks: YES
- Include unsubscribe: YES (required)
- Timezone: User's timezone

## A/B Testing

3 subject variants:
- Variant A: 33%
- Variant B: 33%
- Variant C: 34%

No winner selection - runs full campaign

## Follow-Up Sequence

**Email 1 (Initial):**
- Send immediately
- Body: Main email
- Subject: A/B test variant

**Email 2 (Day 3):**
- If no reply after 3 days
- Subject: `Re: {{original_subject}}`
- Body: Follow-up template

**Email 3 (Day 7):**
- If no reply after 7 days
- Subject: `Last note - {{problem_angle}}`
- Body: Final follow-up

## Inbox Distribution

- Campaign 1: Inbox 1 + Inbox 2
- Campaign 2: Inbox 3 + Inbox 4
- Campaign 3: Inbox 5 + Inbox 6

## Pre-Launch: Lite Path Warmup Check

If Lite path, verify warmup using V2 API:

```bash
curl -X GET https://api.instantly.ai/v2/account/emails \
  -H "Authorization: Bearer {API_KEY}"
```

Check warmup status in response for each inbox.

Verify:
- Status: Active
- Days warming: 21+
- Daily warmup emails: 10-20

If under 21 days:
```
❌ INBOXES NOT READY

Warmed: {X} days (need 21+)

Warmup schedule:
- Week 1: 5-10 emails/day
- Week 2: 10-15 emails/day
- Week 3: 15-20 emails/day
- Week 4+: Ready (25/day)

Launch in {Y} days.

[Check status](https://app.instantly.ai/inboxes)
```

## API Request (V2 API)

**Use Instantly V2 API for all campaign operations.**

```bash
curl -X POST https://api.instantly.ai/v2/campaigns \
  -H "Authorization: Bearer {API_KEY}" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "HVAC_Nov2025_JobCosting",
    "from_emails": ["inbox1@domain1.com", "inbox2@domain1.com"],
    "daily_limit": 25,
    "stop_on_reply": true,
    "stop_on_auto_reply": true,
    "track_opens": true,
    "track_clicks": true,
    "timezone": "America/Los_Angeles",
    "schedule": {
      "days": ["monday", "tuesday", "wednesday", "thursday", "friday"],
      "start_hour": 9,
      "end_hour": 17
    }
  }'
```

**Note:** V2 API structure differs from V1. Key changes:
- `from_inboxes` → `from_emails`
- `stop_on_ooo` → `stop_on_auto_reply`
- Leads and sequences added separately via follow-up API calls
- Full V2 docs: https://developer.instantly.ai/api/v2

## Error Handling

**Campaign creation fails:**
```
❌ CAMPAIGN LAUNCH FAILED

Error: {error_message}

Possible causes:
- Invalid API key
- Inbox not connected
- Daily limit exceeded
- Invalid email format

[Instantly Support](https://help.instantly.ai)

Retrying... (Attempt 1 of 2)
```

After 2nd failure:
```
❌ FAILED (2 attempts)

Unable to create: {campaign_name}

Actions:
1. Check Instantly dashboard
2. Verify inbox connections
3. Try manual creation

Continue with other campaigns? (yes/no)
```

## Output

Success:
```
✅ CAMPAIGN LAUNCHED: HVAC_Nov2025_JobCosting

Details:
- 186 leads uploaded
- 3 subject variants
- Follow-ups: Day 3, Day 7
- Sending: 25/day × 2 inboxes
- Start: Within 1 hour
- Est. complete: 4-5 days

📊 URL: https://app.instantly.ai/campaigns/{id}

Emails begin sending within 1 hour.
Check stats in Instantly dashboard.
```

Update `mission-2-progress.md`:
```markdown
## Campaign Status

### HVAC_Nov2025_JobCosting
- Status: Active 🟢
- Leads: 186
- Sent: 0 (starting soon)
- Opens: 0
- Replies: 0
- Interested: 0
- Launched: 2025-11-20 10:45 AM

[Similar for other 2 campaigns...]

All campaigns launched successfully!

Check status: "Show Mission 2 status"
Run diagnostics (after 5+ days): "Run diagnostics"
```
```
