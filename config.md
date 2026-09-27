# change-plan settings

Edit the values below; the skill reads this file on every run.

- `plans_root`: `mydoc/plans` — relative to the repository root. Each feature gets `<plans_root>/<slug>/`. Keep it out of git (`.gitignore` or `.git/info/exclude`): plans are personal working notes, not reviewed artifacts. The skill checks this the first time it creates the folder.
- `default_language`: `en` — plan prose language when neither the invocation nor the feature README names one. Any language name or code works (`en`, `zh`, `ja` …).
