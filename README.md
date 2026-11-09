# Scheduled report (Python)

An agent that wakes on a schedule, fetches some data, writes a short summary and emails it to you. It sleeps the rest of the time, so you pay for the minutes it works.

It is set to run at 07:00 UTC on weekdays. Change `schedule` and `timeZone` in `stackshift.yaml` to suit you. The schedule uses the usual five-part cron form.

## Before it can run

The agent needs three things from you. None of them is in the code.

1. **Where the data is.** Add the address of your data source to the agent's internet list, because an agent can reach only the addresses you allow. In `stackshift.yaml`, add under `agent:`:

   ```yaml
   internet: [data.example.com]
   ```

2. **The data address and your email**, saved as keys for the agent. They are never shown again:

   ```bash
   printf %s "https://data.example.com/summary.json" | stackshift agents secrets set scheduled-report REPORT_SOURCE_URL
   printf %s "you@example.com" | stackshift agents secrets set scheduled-report REPORT_TO
   ```

   If the data source needs a key, add it in the agent's settings under "Add a key sent on the way out". The agent then never holds that key.

3. **A deploy**, after you push the change.

## What is in here

- `agent.py`: the handler. It fetches the data, asks the model for a summary and sends it from the agent's own inbox.
- `stackshift.yaml`: how StackShift runs it. It may use the model `claude-sonnet-5-5`, it has a monthly AI budget, and it may send mail from its own inbox.
- `Dockerfile`: how StackShift builds and runs it. It installs `requirements.txt` and starts `python agent.py`. StackShift needs a Dockerfile in the agent's folder, so keep it when you change the agent.

What it fetches is untrusted. The model never follows orders written in the data, and StackShift asks you before anything built from the data's words goes out. On the level "Ask every time", each email waits for your approval.

## Run it on StackShift

From the dashboard, open Agents and choose New agent. Or from your terminal:

```bash
stackshift agents create --name "Scheduled report" --template scheduled-report --language python --platform mail.agent_inbox.send
```
