# 🛡️ SOAR-EDR Automation Project  
**Integrating Tines, LimaCharlie, and Slack for Automated Endpoint Isolation**

---

## 📘 Overview

This project demonstrates how to automate endpoint containment using **Tines**, **LimaCharlie**, and **Slack**.  
When a detection event is triggered in LimaCharlie, Tines automatically sends a Slack alert that allows an analyst to approve or deny isolation.  
If approved, Tines calls the LimaCharlie API to isolate the affected sensor and verifies the isolation status.

---

## 🧩 Architecture

![Workflow Diagram](https://github.com/mervintab/automation-tines-limacharlie-slack-playbook/blob/main/docs/SOAR-EDR%20Playbook-drawio.png)

### **Workflow Overview**
1. **LimaCharlie Detection** → Sends webhook to **Tines**  
2. **Tines** → Posts alert message to **Slack**  
3. **Analyst** → Clicks the link to open a **Tines Page**  
4. **User Prompt** → Analyst chooses **Isolate / Skip**  
5. **Tines** → Calls **LimaCharlie API** to isolate the device  
6. **Verification** → Tines checks status and notifies **Slack**

---

## ⚙️ Requirements

| Component | Purpose |
|------------|----------|
| **Tines Workspace** | Builds and runs automation playbooks |
| **LimaCharlie Account** | Endpoint detection and response (EDR) |
| **Slack App** | Delivers alerts and analyst prompts |
| **Python 3.9+** | Optional for local testing of API scripts |

---

## 🚀 Setup Instructions

### **1️⃣ Create a Credential in Tines**
| Field | Value |
|--------|--------|
| **Name** | `LC_API_SECRET` |
| **Type** | Text |
| **Value** | Your LimaCharlie **Org Secret** |
| **Allowed Domain** | `https://jwt.limacharlie.io` |

---

### **2️⃣ Build These Key Actions**
- `Get LC JWT` → Mints short-lived authentication token  
- `Isolate Sensor` → Calls LimaCharlie API to isolate endpoint  
- `Get Isolation Status` → Confirms isolation succeeded

---

### **3️⃣ Create a Tines Page for the Analyst Decision**
| Field | Type | Visibility |
|--------|------|-------------|
| `isolate` | Boolean | Visible |
| `sid` | Text | Hidden |
| `hostname` | Text | Hidden |
| `link` | Text | Hidden |

---

### **4️⃣ Link the Page to Your Slack Alert**

Use a link like this in your **Slack message (Plain Code)** section:
"https://<tenant>.tines.com/pages/<page_id>?sid=<<retrieve_detection.body.routing.sid>>&hostname=<<retrieve_detection.body.routing.hostname>>&link=<<retrieve_detection.body.link>>"


When the analyst clicks the link in Slack, this opens the Page and pre-fills the relevant data.

---

### **5️⃣ Add Decision Triggers**
- `user_prompt.body.isolate == true` → **Isolate Sensor → Get Status → Slack Confirmation**  
- `user_prompt.body.isolate == false` → **Slack “Not Isolated” message**

---

## 🧠 Example Detection Payload
![Lazagne Detction](config/lazagne_detection.json)


