# Mail Assistant

`@eduwork/dsh-mail` · v0.2.5 · MulanPSL-2.0 AND MIT

A security-focused mail assistant for DeepSeek Harness: it reads mail over standard IMAP and sends plain-text mail over standard SMTP, while deliberately staying out of the way of a full mail client. Reading and sending are separate capabilities that stay off until you turn them on, credentials live in the DSH credential service, and every side-effecting action goes through DSH approval.

It is meant for people who want the agent to look something up in their mailbox, summarize a thread, or prepare a reply from inside a conversation — not for mailbox management. The plugin reuses native DSH services (credentials, settings, tools, permission presets, filesystem, client loader) instead of forking the client.

## Highlights

- Exposes exactly five model tools: `mail_list_folders`, `mail_find`, `mail_read`, `mail_get_attachment` and `mail_send`.
- Pages search results with an opaque cursor and an explicit `hasMore`/`nextCursor`, up to 50 messages per page, so an agent can keep paging to full coverage instead of treating the first page as the whole mailbox.
- Opens IMAP mailboxes with read-only `EXAMINE`/read-only locks and never calls `STORE`, `COPY`, `MOVE` or `EXPUNGE`, so reading cannot change the seen state.
- Keeps the IMAP password or app password in the DSH credential provider under the fixed reference `DSH_MAIL_ASSISTANT_PASSWORD`, never in `settings.yaml` and never readable back from the browser.
- Marks message bodies, subjects, addresses and attachment names as untrusted external data that cannot authorize sending, request secrets or change system rules.
- Gates `mail_send` behind an interactive approval under ordinary permissions (Full Access runs without prompting) and still requires the send capability to be enabled separately.
- Saves an attachment only as a regular file inside the current session workspace, with a size cap, a random name and no overwrite.
- Optionally publishes new-mail reminders through the plugin notification protocol, reusing the same read-only IMAP access and in-memory watermarks.

## Install

Install this directory as a local DSH plugin bundle. From a shell:

```bash
dsh plugin --profile web add /absolute/path/to/dsh-mail
```

Or add the directory through the **Plugins** panel in DSH (local bundle). Restart the DSH app after installation; plugins are activated from the bundle patch (`cordis.patch.yml`).

## Configuration

No configuration keys: the bundle patch inserts the plugin with defaults. The plugin installs with read and send disabled and makes no network connection at boot; the normal surface is **Settings → Mail assistant**, and a headless deployment may override the non-secret keys on the patch row. The password belongs in the `DSH_MAIL_ASSISTANT_PASSWORD` credential reference, never in the patch.

| Key | Default | Meaning |
| --- | --- | --- |
| `readEnabled` | `false` | Allow the agent to read mail |
| `sendEnabled` | `false` | Allow the agent to send mail |
| `email` | `""` | Mailbox address |
| `username` | `""` | Login user name; falls back to the address |
| `fromName` | `""` | Optional display name on outgoing mail |
| `inboxFolder` | `INBOX` | Inbox folder path |
| `imapHost` | `""` | IMAP server host name |
| `imapPort` | `993` | IMAP port (1–65535) |
| `imapTls` | `implicit` | `implicit` or `starttls` |
| `smtpHost` | `""` | SMTP submission host name |
| `smtpPort` | `465` | SMTP port (1–65535) |
| `smtpTls` | `implicit` | `implicit` or `starttls` |
| `maxBodyChars` | `20000` | Characters of body text returned per message (1000–200000) |
| `maxMessageBytes` | `26214400` | Accepted message size in bytes (1 MiB–100 MiB) |
| `maxAttachmentBytes` | `20971520` | Attachment size cap in bytes (1 KiB–100 MiB) |
| `notifyEnabled` | `false` | Publish new-mail reminders over the notification protocol |
| `notifyIntervalSeconds` | `120` | Reminder poll interval (30–3600) |
| `notifyExcerptChars` | `160` | Excerpt length in a reminder (0–400) |

## Requirements

- DeepSeek Harness >=0.2.1 <0.3.0
- Node >=24.18.0
- Peer packages: @deepseek-ai/cordis, @deepseek-ai/dsh-api-remotes, @deepseek-ai/dsh-client-ui-renderer, @deepseek-ai/dsh-client-ui-settings, @deepseek-ai/dsh-credentials, @deepseek-ai/dsh-permission-presets, @deepseek-ai/dsh-tools, @deepseek-ai/dsh-client-locale, react
- A profile that already provides the official Settings, Credentials, Tools, Filesystem, Permission Presets, Approval and Web Client capabilities

## Notes and limits

- Scope is deliberately narrow: no delete, move, archive, label, read/unread change, contacts, rules, auto-reply, HTML mail authoring or full mailbox UI, and no copy of sent mail into the IMAP Sent folder — server policy decides whether sent mail is retained.
- Authentication is user name plus password or app password over implicit TLS or STARTTLS only, with certificate verification not switchable off. XOAUTH2/OIDC mailbox login is not implemented, so Gmail, Microsoft 365 and similar providers work only where tenant policy permits app passwords and SMTP AUTH.
- `mail_get_attachment` writes through the local Host filesystem after path resolution and containment re-checks, because DSH exposes no binary-write seam; remote container or non-shared filesystem providers should expect a clear failure for that tool while other capabilities keep working. A send that loses the connection during DATA returns `unknown` and must not be retried automatically.
- This directory is a source snapshot: no `lib/` build output is included, so the `lib/index.js` entry declared in `package.json` does not exist yet. Build the package in the EduWork tree before installing, otherwise DSH reports a missing bundle entry.

## 中文文档

[README.zh-CN.md](./README.zh-CN.md)

## License

MulanPSL-2.0 AND MIT — see [LICENSE](./LICENSE) and, where present, [LICENSE-MIT](./LICENSE-MIT). Extracted from the ECNU EduWork distribution (https://github.com/ECNU/EduWork); upstream copyright and license notices are preserved.
