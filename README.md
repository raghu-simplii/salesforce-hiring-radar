# Salesforce hiring radar

Public 7-day and 15-day Salesforce hiring scan.

Live page: https://raghu-simplii.github.io/salesforce-hiring-radar/

## Config

Edit `config.json` to change companies, add companies, change the keyword, or change lookback windows (`windows_days`). Then the weekday refresh (or a chat ask) rescans and republishes this page.

```json
{
  "keyword": "Salesforce",
  "end_date": "today",
  "windows_days": [7, 15],
  "default_window_days": 7,
  "companies": [
    { "name": "Persistent Systems", "short": "Persistent", "aliases": [], "linkedin": "https://www.linkedin.com/company/persistent-systems" },
    { "name": "Acuity Analytics", "short": "Acuity", "aliases": ["Acuity Knowledge Partners"], "linkedin": "https://www.linkedin.com/company/acuityanalytics" }
  ]
}
```
