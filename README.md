# 📅 Daily Reminder

A **Microsoft Power Automate** flow that automatically sends daily task reminder cards to Microsoft Teams group chats — every day except Sunday — at the end of each working shift.

---

## 📌 Overview

This flow helps teams stay on top of their daily tasks by sending a formatted **Adaptive Card** reminder to one or more Microsoft Teams group chats at a scheduled time. It intelligently skips Sundays to respect non-working days.

---

## ✨ Features

- ⏰ **Scheduled daily trigger** — fires automatically once per day at a configured time
- 🚫 **Sunday skip logic** — uses a condition to suppress notifications on Sundays
- 💬 **Adaptive Card messages** — rich, styled reminder cards sent directly to Teams group chats
- 📣 **Multi-chat support** — sends reminders to multiple group chats in sequence
- 🤖 **Flow bot poster** — messages appear from the Flow bot, keeping things clean and professional

---

## 🗂️ Project Structure

```
daily eminder/
├── manifest.json          # Package metadata and resource declarations
├── apisMap.json           # Maps connector references to resource IDs
├── connectionsMap.json    # Maps connection references to resource IDs
├── definition.json        # Full flow definition (logic, triggers, actions)
└── README.md              # This file
```

---

## 🔧 Prerequisites

Before importing this flow, make sure you have:

- A **Microsoft 365** account with access to **Power Automate** ([flow.microsoft.com](https://flow.microsoft.com))
- **Microsoft Teams** connector enabled in your Power Automate environment
- A valid **Microsoft Teams connection** authenticated with your account
- Access to the **Teams group chats** you want to send reminders to

---

## 🚀 Setup & Import Guide

### Step 1 — Get the Group Chat Thread IDs

You need the thread IDs for each Teams group chat that should receive the reminder.

1. Open Microsoft Teams and navigate to the target group chat.
2. Click the three-dot menu (`...`) → **Get link to chat**.
3. The URL will contain the thread ID in the format `19:xxxxxxxx@thread.v2`. Copy this value.
4. Repeat for each group chat.

### Step 2 — Update the Definition File

Open `definition.json` and replace all placeholder values:

| Placeholder | What to replace it with |
|---|---|
| `YOUR_FLOW_ID` | A new UUID for your flow (or leave for auto-generation) |
| `YOUR_CREATOR_USER_ID` | Your Azure AD user object ID |
| `YOUR_TENANT_ID` | Your Microsoft 365 tenant ID |
| `YOUR_TEAMS_GROUP_CHAT_THREAD_ID_1` | Thread ID for the first group chat |
| `YOUR_TEAMS_GROUP_CHAT_THREAD_ID_2` | Thread ID for the second group chat |
| `YOUR_TEAMS_CONNECTION_NAME` | Your Teams connection name from Power Automate |
| `YOUR_ACTION_METADATA_ID_1/2` | New UUIDs (generate freely) |
| `YOUR_CONDITION_METADATA_ID` | New UUID |
| `YOUR_TRIGGER_METADATA_ID` | New UUID |

> 💡 You can generate UUIDs at [uuidgenerator.net](https://www.uuidgenerator.net/).

### Step 3 — Update the Manifest File

Open `manifest.json` and replace:

| Placeholder | What to replace it with |
|---|---|
| `YOUR_PACKAGE_TELEMETRY_ID` | A new UUID for telemetry tracking |
| `YOUR_FLOW_RESOURCE_ID` | Same UUID used as `YOUR_FLOW_ID` |
| `YOUR_TEAMS_API_RESOURCE_ID` | A new UUID for the Teams API resource |
| `YOUR_TEAMS_CONNECTION_RESOURCE_ID` | A new UUID for the Teams connection resource |
| `YOUR_TEAMS_ACCOUNT_EMAIL` | The email of the Microsoft account used for the Teams connection |

### Step 4 — Update the Maps Files

In `apisMap.json` and `connectionsMap.json`, replace the placeholder values with the same UUIDs used in `manifest.json`.

### Step 5 — Import to Power Automate

1. Go to [flow.microsoft.com](https://flow.microsoft.com) and sign in.
2. Navigate to **My Flows** → **Import** → **Import Package (Legacy)**.
3. Upload this project folder as a `.zip` archive.
4. During import, map the **Microsoft Teams connection** to your existing authenticated connection (or create a new one).
5. Click **Import** and wait for confirmation.

### Step 6 — Test the Flow

1. Open the imported flow in Power Automate.
2. Click **Test** → **Manually** to trigger a test run.
3. Verify that the Adaptive Card appears in both group chats.
4. Check the run history to confirm all steps succeeded.

---

## ⚙️ Configuration

### Changing the Schedule

The flow triggers daily at **17:59 Singapore Standard Time (SGT)**. To change this:

1. Open the flow in Power Automate.
2. Click on the **Recurrence** trigger.
3. Update the `hours`, `minutes`, or `timeZone` fields as needed.

### Changing the Skip Day

Currently, the flow skips **Sunday**. To change the skip day, edit the condition in `SkipSundayCondition`:

```json
"equals": ["@formatDateTime(utcNow(), 'dddd')", "Sunday"]
```

Replace `"Sunday"` with any other day name (e.g., `"Saturday"`).

### Customizing the Message

The reminder message is an **Adaptive Card** defined inside each action's `body/messageBody` parameter. You can customize it using the [Adaptive Card Designer](https://adaptivecards.io/designer/).

### Adding More Group Chats

To send reminders to additional group chats, duplicate the `PROGJECT-ASSIGNMENT` action inside `SkipSundayCondition` and update the `body/recipient` with the new thread ID. Chain actions using `runAfter`.

---

## 🔄 Flow Logic

```
Recurrence Trigger (Daily at 17:59 SGT)
        │
        ▼
SkipSundayCondition
   ├─ TRUE (Not Sunday)
   │       │
   │       ▼
   │    DATES (Compose today's date string)
   │       │
   │       ▼
   │    MS_TEAMS_GC < PLACEHOLDER
   │    (Send Adaptive Card → Group Chat 1)
   │       │
   │       ▼
    │    MS_TEAMS_GC < PLACEHOLDER
   │    (Send Adaptive Card → Group Chat 2)
   │
   └─ FALSE (Sunday)
           │
           ▼
        Compose ("Skipped notification: Today is Sunday.")
```

---

## 🃏 Adaptive Card Preview

The reminder card sent to Teams looks like this:

```
╔══════════════════════════════════════╗
║     🚨 REMINDER TO EVERYONE 🚨      ║
║       Today is Monday, 2026-05-25    ║
║  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━    ║
║  Please ensure that you submit your  ║
║  task before the end of your shift.  ║
║             Thank you!               ║
╚══════════════════════════════════════╝
```

---

## 🔒 Security Notes

- **Never commit real IDs, tenant IDs, or email addresses** to a public repository. All sensitive values in this repo have been replaced with `YOUR_*` placeholders.
- The `$authentication` parameter is handled securely by Power Automate at runtime and should never be hardcoded.
- Store any sensitive configuration in **Power Automate environment variables** or your organization's secret manager.

---

## 🤝 Contributing

1. Fork this repository.
2. Create a feature branch: `git checkout -b feature/your-feature-name`.
3. Commit your changes: `git commit -m "Add your feature"`.
4. Push to the branch: `git push origin feature/your-feature-name`.
5. Open a Pull Request.

---

## 📄 License

This project is licensed under the [MIT License](LICENSE).

---

## 📬 Contact

For questions or issues, open a GitHub Issue or reach out through your organization's internal channels.
