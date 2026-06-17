# CST8919 Lab 2： Azure Monitoring

Student Name: Bosi Chen\
Student ID: 041040774\
Course: CST8919 DevOps - Security and Compliance\
Semester: Summer 2026

---

## Video Demo

[Video Demo](https://youtu.be/nzKiyQuAts4)

---

## Overview

This lab includes deploying a Flask application with Auth0 authentication to Azure Web App and monitoring application logs using Azure Monitor.

## What I learned

- Deploy Flask applications to Azure App Service
- Configure Log Analytics Workspace
- Query application logs using KQL
- Create alert rules based on log queries

## Challenges

1. I tried to deploy the application from a previous lab first, but the authentication configuration was tightly coupled to the local environment and required changes to work correctly in Azure. Since Lab 1 was a separate assignment, I decided to create a new repository for Lab 2 rather than making changes to the original Lab 1 project.

2. Another challenge was configuring Auth0 for deployment. After deployment, the callback URLs, logout URLs, and allowed web origins had to be updated to the Azure Web App domain. Troubleshooting the authentication flow required multiple deployment and testing cycles before login and logout functionality worked reliably in the Azure environment.

3. I also had some difficulties while writing the KQL query. My application could not generate authentication failure events during normal testing since it was verified by Auth0. Instead, I used HTTP 400 responses from the Auth0 callback endpoint as indicators of failed authentication attempts. This allowed me to work with log analysis and alerts while still working within the available log data.

## KQL Queries

```kusto
AppServiceHTTPLogs
| order by TimeGenerated desc
```

This one is simply retrieving all the HTTP requests made in the selected time range, ordered by most recent to oldest.

```kusto
AppServiceHTTPLogs
| where CsUriStem contains "/callback"
| where ScStatus == 400
| project TimeGenerated, CsUriStem, ScStatus
| order by TimeGenerated desc
```

This query searched the AppServiceHTTPLogs table for requests whith the Auth0 `/callback` endpoint, and returned with an HTTP 400 status code. Then let it display only the timestamp, request path, and status code columns and order from newest to oldest. I used these events to indicate failed login attempts.