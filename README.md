# n8n Automation Workflows

Production n8n workflows I built for QC reporting, HR approvals and facility monitoring. Each one replaces a manual daily task with an automated process.

![n8n](https://img.shields.io/badge/n8n-EA4B71?style=flat-square&logo=n8n&logoColor=white)
![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=flat-square&logo=javascript&logoColor=black)
![MySQL](https://img.shields.io/badge/MySQL-4479A1?style=flat-square&logo=mysql&logoColor=white)
![InfluxDB](https://img.shields.io/badge/InfluxDB-22ADF6?style=flat-square&logo=influxdb&logoColor=white)
![Google Sheets](https://img.shields.io/badge/Google_Sheets-34A853?style=flat-square&logo=googlesheets&logoColor=white)
![Gmail](https://img.shields.io/badge/Gmail-EA4335?style=flat-square&logo=gmail&logoColor=white)
![Telegram](https://img.shields.io/badge/Telegram-26A5E4?style=flat-square&logo=telegram&logoColor=white)

> All company names, credentials, IDs, hostnames and tokens have been removed. Values to fill in are marked `YOUR_...`.

## Workflows

| | Workflow | Category | Trigger | Highlights |
|---|---|---|---|---|
| ⭐ | [Daily QC Report (PDF to Telegram)](workflows/reporting) | Reporting | Daily at 07:00 | MySQL, conditional logic, HTML→PDF with Gotenberg, Telegram |
| ⭐ | [Early-Leave Approval Request](workflows/hr-automation) | HR Automation | Webhook (form) | Google Sheets, approver lookup, HTML email with Approve/Reject buttons |
| ⭐ | [Temperature Monitoring](workflows/monitoring) | Monitoring | Every hour | InfluxDB sensor data, stale-signal detection, formatted Telegram report |

---

## 📊 Daily QC Report (PDF to Telegram)

QC staff record cut-check inspections during the night shift. Every morning this workflow summarises the previous day's results into two PDF reports, one per product line, and posts them to a Telegram group.

```mermaid
flowchart LR
    A["⏰ Schedule<br/>07:00 daily"] --> B["Code<br/>yesterday's date"]
    B --> C["MySQL<br/>count records"]
    C --> D{"IF<br/>count > 0"}
    D -- true --> E1["MySQL<br/>Product A data"] --> F1["Code<br/>summarise + HTML"] --> G1["Gotenberg<br/>HTML → PDF"] --> H1["Telegram<br/>send PDF"]
    D -- true --> E2["MySQL<br/>Product B data"] --> F2["Code<br/>summarise + HTML"] --> G2["Gotenberg<br/>HTML → PDF"] --> H2["Telegram<br/>send PDF"]
```

[Details →](workflows/reporting)

## 👥 Early-Leave Approval Request

Employees submit an early-leave request through a web form. The workflow saves it, finds the employee's head of department, and emails them the request with **Approve** and **Reject** buttons.

```mermaid
flowchart LR
    A["🌐 Webhook<br/>form submitted"] --> B["Respond<br/>to form"] --> C["Code<br/>clean data, ID, deadline"]
    C --> D["Google Sheets<br/>append request"]
    D --> E["Google Sheets<br/>look up HOD email"]
    D --> M["Merge"]
    E --> M --> G["Gmail<br/>Approve / Reject email"]
```

[Details →](workflows/hr-automation)

## 🌡️ Temperature Monitoring

Every hour, the workflow reads the latest readings from about 30 sensors (chillers, cold rooms, blast freezers, loading bays and production areas) and posts a formatted status report to Telegram.

```mermaid
flowchart LR
    A["⏰ Schedule<br/>every hour"] --> B["HTTP Request<br/>InfluxDB Flux query"] --> C["Code<br/>parse + detect lost signals"] --> D["Code<br/>build message"] --> E["Telegram<br/>status report"]
```

[Details →](workflows/monitoring)

---

## How to Import

1. In n8n, open **Workflows → Import from File** and choose a `.json` file from `workflows/`.
2. Connect your own credentials (MySQL, Google Sheets, Gmail, Telegram) on the nodes that need them.
3. Replace every `YOUR_...` placeholder with your own values.
4. Test it with **Execute workflow**, then activate it.

## Repository Structure

```
workflows/
├── reporting/       # Daily QC Report (PDF to Telegram)
├── hr-automation/   # Early-Leave Approval Request
└── monitoring/      # Temperature Monitoring
```
