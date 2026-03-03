# Mail Reference

## Listing

```
python graph-office-suite/scripts/mail_fetch.py --folder Inbox --top 10 --unread
```

- `--filter` accepts OData expressions (`contains(subject,'Status')`).
- `--id <messageId>` fetches a specific message.
- `--include-body` returns full body content (HTML + text).
- `--mark-read` and `--move-to <folderId>` act on the message loaded with `--id`.

## Sending

```
python graph-office-suite/scripts/mail_send.py \
  --to user@example.com \
  --subject "Follow-up" \
  --body-file drafts/reply.html --html \
  --cc teammate@example.com \
  --attachment docs/proposal.pdf
```

- `saveToSentItems` is `True` by default. Use `--no-save-copy` to disable.
- Attachments are sent as `fileAttachment` and are limited on this endpoint; for large files, implement upload session flow.

## Useful folder IDs

List folders with:
```
curl -H "Authorization: Bearer <ACCESS_TOKEN>" \
  https://graph.microsoft.com/v1.0/me/mailFolders
```
Or query a known folder with `mail_fetch.py --folder SentItems`.

## De-duplication / watch

Use `state/email_watch.json` to track the `receivedDateTime` and ID of the last processed message.
