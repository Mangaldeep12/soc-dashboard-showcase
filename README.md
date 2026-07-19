<div align="center">

# 🛡️ SOC Dashboard

### A unified SIEM + SOAR security-operations console

*One pane of glass over Elastic · Wazuh · Zabbix — with automated P1 response, MITRE ATT&CK mapping, attack-surface scanning, and an AI analyst assistant.*

![React](https://img.shields.io/badge/React-18-61DAFB?logo=react&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-strict-3178C6?logo=typescript&logoColor=white)
![Vite](https://img.shields.io/badge/Vite-5-646CFF?logo=vite&logoColor=white)
![FastAPI](https://img.shields.io/badge/FastAPI-SOAR-009688?logo=fastapi&logoColor=white)
![Elastic](https://img.shields.io/badge/Elastic-SIEM-005571?logo=elasticstack&logoColor=white)
![Wazuh](https://img.shields.io/badge/Wazuh-XDR-3268C4)
![MITRE ATT&CK](https://img.shields.io/badge/MITRE-ATT%26CK-c00)
![License](https://img.shields.io/badge/license-Lab%20%2F%20Educational-informational)

</div>

---

![Overview](screenshots/01-overview.png)

## Overview

**SOC Dashboard** is a full-stack Security Operations Center built around a single question: *"is this real, and does it matter — and can we respond automatically?"*

It ingests telemetry from Linux + Windows endpoints and a network gateway, **correlates** attacker behaviour across sources into a single kill-chain incident, maps it to **MITRE ATT&CK**, drives an **automated P1 response** (SOAR) behind hard safety rails, and presents everything through a fast, 16-page React console with a built-in **AI analyst assistant**.

The flagship scenario it detects end-to-end: a single source runs network recon (port scan) then an SSH/RDP **brute force** against the same host within minutes — the classic *Recon → Brute Force* kill chain — which is correlated, prioritized **P1**, auto-contained, and written up as an incident with an AI triage summary.

---

## ✨ Key capabilities

| Domain | What it does |
|--------|--------------|
| **Unified SIEM** | Elastic Stack + Wazuh + Zabbix normalized into one console — network (Snort), host (Sysmon/Wazuh), and infrastructure telemetry side by side |
| **Correlation** | Cross-source *Recon → Brute Force* kill-chain detection on a single attacker IP, not dozens of low alerts |
| **Auto-Response (SOAR)** | P1 chain — *enrich → decide → contain → notify → record → auto-expire* — with **dry-run default**, kill-switch, never-block allowlist, and 60-min TTL auto-unblock |
| **Detection-as-Code** | Sigma rules + CI/CD pipeline (validate → convert → test → deploy) |
| **Continuous validation** | Atomic Red Team harness proves each detection still fires and flags security drift |
| **Threat intelligence** | IOC enrichment + reputation scoring that drives priority (malicious → force P1) |
| **Attack-surface scanning** | Host (OpenVAS), web (ZAP), API (Nuclei), **mobile (MobSF)** and **external subdomain** recon → aggregated findings → *"vulnerable AND targeted"* auto-escalation |
| **Case management** | IR cases with tasks, evidence, and lifecycle |
| **AI assistant** | A floating "Copilot" + a full analysis page — local open-source model (offline, private) with longer-form incident/attacker/posture/hunt analysis |
| **MITRE ATT&CK** | Live coverage heatmap + per-technique detection mapping and gap analysis |

Every table across the console **drills down** — click any row for a full detail panel (rule logic, raw log evidence, threat-intel verdict, correlated incident, recommended action). Light + dark themes throughout.

---

## 🖼️ Screenshots

### Monitor
| Live Alert Stream | Attack Investigation |
|---|---|
| ![Alert Stream](screenshots/02-alert-stream.png) | ![Attacks](screenshots/03-attacks.png) |

**Live Telemetry wall** — streaming ingest, alert volume, protocol radar, and a time-brush CPU explorer
![Telemetry](screenshots/04-telemetry.png)

| Threats (attacker intel) | Top Source IPs → the attacker surfaces |
|---|---|
| ![Threats](screenshots/05-threats.png) | *(see Overview)* |

### Respond
**Auto-Response (SOAR)** — the P1 containment chain, live action log, and blocked-IP list with TTL countdown
![Auto-Response](screenshots/07-auto-response.png)

| Incident Queue | Case Management + AI triage |
|---|---|
| ![Incidents](screenshots/06-incidents.png) | ![Cases](screenshots/08-cases.png) |

### Analyze
| Detection Rules (+ add-rule) | Detection Health (Sigma + validation) |
|---|---|
| ![Detections](screenshots/09-detections.png) | ![Detection Health](screenshots/10-detection-health.png) |

| Threat Intelligence | Vulnerabilities & Attack Surface |
|---|---|
| ![Threat Intel](screenshots/11-threat-intel.png) | ![Attack Surface](screenshots/12-attack-surface.png) |

**MITRE ATT&CK coverage** — tactic × technique heatmap with per-technique detection mapping
![MITRE](screenshots/13-mitre.png)

**AI Assistant** — model picker + longer-form incident / attacker / posture / hunt analysis
![AI Assistant](screenshots/16-ai-assistant.png)

### Infrastructure
| Infra Health (Zabbix) | Endpoint / sensor fleet |
|---|---|
| ![Infra](screenshots/14-infra.png) | ![Endpoints](screenshots/15-endpoints.png) |

---

## 🏗️ Architecture

```mermaid
flowchart LR
  subgraph Endpoints
    LNX["lnx-victim<br/>Filebeat · Wazuh · Zabbix"]
    WIN["win-victim<br/>Sysmon · Winlogbeat · Wazuh"]
  end
  GW["soc-gw<br/>Snort IDS"]
  ATT["kali-attacker"] -. recon + brute force .-> LNX & WIN

  subgraph Core["soc-core"]
    ES[("Elasticsearch")]
    WZ[("Wazuh")]
    ZBX[("Zabbix")]
    SOAR["SOAR Responder<br/>(FastAPI)"]
    AI["Local AI Gateway"]
  end

  LNX & WIN & GW --> ES
  LNX & WIN --> WZ
  LNX & WIN --> ZBX
  WZ -- "P1 alert" --> SOAR
  SOAR -- "enrich → decide → contain<br/>→ notify → record → expire" --> WZ
  ES & WZ & ZBX --> GRAF["Grafana OSS<br/>unified pane"]
  ES & WZ & ZBX --> UI["React SOC Dashboard<br/>+ AI Copilot"]
  AI --- UI
```

**Detection → response flow:** telemetry → SIEM correlation → priority (P1–P4) → *for P1*, the SOAR chain auto-enriches (threat intel), decides (allowlist + kill-switch), contains (Wazuh Active Response firewall-drop), notifies, records an incident, and schedules a TTL auto-unblock — all **dry-run by default**.

---

## 🧰 Tech stack

| Layer | Technology |
|-------|-----------|
| **Frontend** | React 18 · TypeScript (strict) · Vite · Recharts · React Router · light/dark theming |
| **SIEM / telemetry** | Elastic Stack (Elasticsearch + Kibana) · Wazuh (XDR/HIDS) · Zabbix · Snort · Sysmon · Filebeat / Winlogbeat |
| **SOAR** | FastAPI responder · Wazuh Active Response · HMAC-signed webhooks · Mattermost / Slack / email notifications |
| **Detection engineering** | Sigma (detection-as-code) + CI/CD · Atomic Red Team (continuous validation) · MITRE ATT&CK mapping |
| **Threat intel** | MISP · AbuseIPDB · OTX · URLhaus enrichment + scoring |
| **Vuln / attack surface** | OpenVAS · OWASP ZAP · Nuclei · MobSF · subfinder/httpx · DefectDojo aggregation |
| **Case management** | DFIR-IRIS |
| **AI** | Local open-source LLM (Ollama) — offline & private — for chat, triage, and longer-form analysis |
| **Ops** | Docker Compose (layered) · isolated `10.10.10.0/24` lab network |

---

## 📊 Detection coverage (highlights)

| Attack | Sensor(s) | MITRE technique |
|--------|-----------|-----------------|
| Network port scan | Snort port-scan inspector | **T1046** Network Service Discovery |
| SSH brute force | Wazuh + Elastic threshold + Zabbix | **T1110** Brute Force |
| Windows logon brute force / spray | Sysmon + Winlogbeat (4625) + Wazuh | **T1110.001 / .003** |
| **Recon → Brute Force kill chain** | Snort + Wazuh + Elastic (correlated) | **T1046 + T1110** → **P1** |
| Malicious account creation | Sysmon (net.exe) + Wazuh | **T1136.001** Create Account |

---

## 📦 Source code

The full application source lives in a **private repository**: [`Mangaldeep12/soc-dashboard`](https://github.com/Mangaldeep12/soc-dashboard) *(private — access on request)*.

This showcase repository contains the project overview and screenshots only.

---

## ⚠️ Disclaimer

This is an **isolated lab / educational project**. All credentials are placeholders, all offensive tooling and attack simulations are for **authorized use only** on assets you own or are permitted to test, and the environment runs on a segregated network with no route to production or the internet.

<div align="center">
<sub>Built as a self-contained SOC engineering lab · Detection engineering · SOAR automation · Threat-informed defense</sub>
</div>
