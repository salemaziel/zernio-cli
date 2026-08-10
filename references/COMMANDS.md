# Zernio CLI — Full Command Reference

Detailed per-command reference for inbox, contacts, broadcasts, sequences, and automations. SKILL.md keeps the overview and core workflow; load this file only when you need the full flag-level detail for these areas.

## Inbox (DMs, Comments, Reviews)

```bash
# List DM conversations
zernio inbox:conversations --platform instagram
zernio inbox:conversations --accountId <id> --status active

# Read messages in a conversation
zernio inbox:messages <conversationId> --accountId <id>

# Send a DM
zernio inbox:send <conversationId> --accountId <id> --message "Thanks for reaching out!"

# List comments across posts
zernio inbox:comments --platform instagram --since "2025-01-01"

# Get comments on a specific post
zernio inbox:post-comments <postId> --accountId <id>

# Reply to a comment
zernio inbox:reply <postId> --accountId <id> --message "Thank you!"

# Reply to a specific comment (not the post)
zernio inbox:reply <postId> --accountId <id> --message "Great point" --commentId <commentId>

# List reviews (Facebook, Google Business)
zernio inbox:reviews --minRating 1 --maxRating 3 --hasReply false

# Reply to a review
zernio inbox:review-reply <reviewId> --accountId <id> --message "Thanks for your feedback!"
```

## Contacts

```bash
# List contacts
zernio contacts:list --profileId <id>
zernio contacts:list --search "john" --tag vip --platform instagram

# Create a contact
zernio contacts:create --profileId <id> --accountId <id> --platform instagram --platformUserId <userId> --name "John Doe" --tags "vip,lead"

# Get/update/delete
zernio contacts:get <contactId>
zernio contacts:update <contactId> --name "Jane Doe" --tags "vip,customer"

# Delete a contact — verify it exists first
zernio contacts:get <contactId>          # confirm the right contact
zernio contacts:delete <contactId>

# List channels (platforms) for a contact
zernio contacts:channels <contactId>

# Custom fields
zernio contacts:set-field <contactId> <slug> --value "some value"
zernio contacts:clear-field <contactId> <slug>

# Bulk create from JSON file (up to 1000) — check the file/count before bulk insert
zernio contacts:list --profileId <id>     # see what already exists to avoid dupes
zernio contacts:bulk-create --profileId <id> --accountId <id> --platform instagram --file ./contacts.json
```

## Broadcasts

```bash
# List broadcasts
zernio broadcasts:list --profileId <id> --status draft

# Create a broadcast draft (generic message)
zernio broadcasts:create --profileId <id> --accountId <id> --platform instagram --name "Summer Sale" --message "Check out our summer deals!"

# Create a WhatsApp broadcast with template
zernio broadcasts:create --profileId <id> --accountId <id> --platform whatsapp --name "Order Update" --templateName "order_confirmation" --templateLanguage "en"

# Add recipients
zernio broadcasts:add-recipients <broadcastId> --contactIds <id1>,<id2>,<id3>
zernio broadcasts:add-recipients <broadcastId> --useSegment

# Send immediately or schedule — check recipient count before sending
zernio broadcasts:recipients <broadcastId>   # confirm who will receive it
zernio broadcasts:send <broadcastId>
zernio broadcasts:schedule <broadcastId> --scheduledAt "2025-06-01T10:00:00Z"

# Check delivery status
zernio broadcasts:get <broadcastId>
zernio broadcasts:recipients <broadcastId> --status delivered

# Cancel
zernio broadcasts:cancel <broadcastId>
```

## Sequences (Drip Campaigns)

```bash
# List sequences
zernio sequences:list --profileId <id> --status active

# Create a sequence (steps defined in JSON file)
zernio sequences:create --profileId <id> --accountId <id> --platform instagram --name "Welcome Series" --stepsFile ./steps.json

# steps.json example:
# [
#   {"order": 1, "delayMinutes": 0, "message": "Welcome! Thanks for connecting."},
#   {"order": 2, "delayMinutes": 1440, "message": "Here are some tips to get started..."},
#   {"order": 3, "delayMinutes": 4320, "message": "Check out our latest content!"}
# ]

# Activate/pause
zernio sequences:activate <sequenceId>
zernio sequences:pause <sequenceId>

# Enroll contacts — check recipient count before enrolling
zernio sequences:enrollments <sequenceId>   # see who is already enrolled
zernio sequences:enroll <sequenceId> --contactIds <id1>,<id2>

# Check enrollments
zernio sequences:enrollments <sequenceId> --status active

# Unenroll a contact
zernio sequences:unenroll <sequenceId> <contactId>
```

## Comment-to-DM Automations

```bash
# List automations
zernio automations:list --profileId <id>

# Create: auto-DM anyone who comments "info" on a post
zernio automations:create \
  --profileId <id> --accountId <id> \
  --platformPostId <igPostId> \
  --name "Lead Magnet" \
  --keywords "info,details,link" \
  --dmMessage "Here's the link you asked for: https://example.com" \
  --commentReply "Check your DMs!"

# Trigger on ALL comments (no keywords filter)
zernio automations:create \
  --profileId <id> --accountId <id> \
  --platformPostId <igPostId> \
  --name "Engagement Boost" \
  --dmMessage "Thanks for engaging! Here's a special offer..."

# Update
zernio automations:update <automationId> --isActive false
zernio automations:update <automationId> --keywords "buy,order" --matchMode exact

# View trigger logs
zernio automations:logs <automationId> --status sent
zernio automations:logs <automationId> --status failed

# Delete
zernio automations:delete <automationId>
```

## Platform Availability

Broadcasts and sequences work across all inbox platforms (Instagram, Facebook, Telegram, X, WhatsApp, Bluesky, Reddit). Comment-to-DM automations currently support Instagram and Facebook.
