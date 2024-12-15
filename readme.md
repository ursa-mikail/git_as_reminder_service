# Step-by-Step Guide to Using GitHub Actions for Reminders
Step 1: Create a New GitHub Repository (if you don’t have one already)
Create a new repository on GitHub where you want to set up your reminders.

Step 2: Create a GitHub Actions Workflow
Navigate to the Actions tab in your repository.
Click on "New workflow" and then on "set up a workflow yourself".

Step 3: Define the Workflow
Create a YAML file for your workflow. This file will define the schedule and actions that will create reminders.

``` % tree .github
.github
├── ISSUE_TEMPLATE
│   └── reminder.md
└── workflows
    └── main.yml

```

```yaml
name: Create Reminder Issues

on:
  schedule:
    # cron: '0 9 15 12 *' # Runs at 09:00 on December 15th (UTC)
    # Runs at 9 AM every day
    # - cron: '0 * * * *' # hourly
    - cron: '*/5 * * * *' # Every 5 minutes'
    # * * * * * Every minute
    # 0 * * * * Every hour
    # 0 0 * * * Every day at 12:00 AM
    # 0 0 * * FRI At 12:00 AM, only on Friday
    # 0 0 1 * * At 12:00 AM, on day 1 of the month
  workflow_dispatch: # This allows you to manually trigger the workflow


jobs:
  create-reminder:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout repository
      uses: actions/checkout@v2

    - name: Check the year
      id: year-check
      run: echo "::set-output name=year::$(date +'%Y')"

    - name: Create Reminder Issue
      #if: steps.year-check.outputs.year == '2025'  # Runs only if the year is 2025
      #uses: ursa-mikail/create-issue-from-file@v4
      #with:
      #  title: 'Daily Reminder'
      #  content-filepath: '.github/ISSUE_TEMPLATE/reminder.md'
      #  labels: reminder
      uses: JasonEtco/create-an-issue@v2
      with:
        filename: '.github/ISSUE_TEMPLATE/reminder.md'
        #title: 'Daily Reminder'
        #content-filepath: '.github/ISSUE_TEMPLATE/reminder.md'
        #labels: reminder
        update_existing: true
        search_existing: open
      env:
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Step 4: Create a Template for the Issue
Create a template for the issues that will be generated. Create a directory structure .github/ISSUE_TEMPLATE/reminder.md and put the content of the issue in the reminder.md file.

```
---
title: "Daily Reminder"
---

## Daily Reminder

This is your daily reminder to check on the tasks and progress for the day.

- [ ] Task 1
- [ ] Task 2
- [ ] Task 3

**Please make sure to update the status of your tasks.**

```

Step 5: Commit and Push the Workflow
Commit your changes and push them to your GitHub repository. The workflow will now run every day at 9 AM UTC, creating an issue titled "Daily Reminder" with the content specified in your template.

## Caveats:

```
As the workflow may not be stable, it is good to set 2 tasks at least:
1. 1 as a liveness test (as confirmation liveness test)
2. the 2nd is the actual task

```
