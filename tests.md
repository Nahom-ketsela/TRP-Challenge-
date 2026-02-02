# 🧪 Agent Behavior & Configuration Tests

This document outlines the test suite executed to verify the **Tenx MCP connection** and the **Behavioral Rules** configured in `.github/copilot-instructions.md`.

## 📋 Test Overview

| Test ID | Name | Goal | Status |
| :--- | :--- | :--- | :--- |
| **T1** | **Plan Mode Verification** | Ensure agent plans before coding on complex tasks. | ✅ PASS |
| **T2** | **Trigger & Tone Check** | Verify mandatory MCP triggers & "2026 Human" persona. | ✅ PASS |
| **T3** | **Anti-Hallucination** | Verify agent refuses to guess about non-existent files. | ✅ PASS |

---

## 🔬 Detailed Test Logs

### 🔹 Test 1: The "Plan Mode" Protocol
* **Objective:** Verify compliance with the "Think Before You Code" rule (Boris Cherny Workflow).
* **Prompt:** `"I want to refactor my project structure to separate logic from UI. How should I start?"`
* **Success Criteria:**
    1.  [x] Agent must call `log_passage_time_trigger` first.
    2.  [x] Agent must **NOT** output code immediately.
    3.  [x] Agent must provide a bulleted "Proposed Plan".
* **Observed Output:**
    > "To refactor your project structure and separate logic from UI, follow these steps:
    >
    > **Plan:**
    > 1. **Analyze Current Structure:** Identify files that mix UI and logic. Categorize components...
    > 2. **Create New Folders:** `components/` for UI components. `services/` or `logic/` for business logic...
    > 3. **Refactor Code:** Move reusable UI components to `components/`. Extract logic...
    > 4. **Update Imports:** Adjust file imports to reflect the new structure.
    > 5. **Test:** Ensure the application works as expected...
    >
    > Would you like me to help with creating the folder structure or refactoring specific files?"
* **Result:** **PASS**. The agent successfully entered "Architect Mode" instead of rushing to implementation.

### 🔹 Test 2: Mandatory Triggers & Persona
* **Objective:** Verify the MCP server logs simple interactions and the agent avoids robotic "bot-speak".
* **Prompt:** `"What is the capital of Ethiopia?"`
* **Success Criteria:**
    1.  [x] Agent must call `log_passage_time_trigger`.
    2.  [x] Response must be direct (No "In conclusion", "I hope this helps").
* **Observed Output:**
    > "The capital of Ethiopia is Addis Ababa."
* **Result:** **PASS**. Response was concise, grounded, and correctly logged.

### 🔹 Test 3: Anti-Hallucination ("Never Speculate")
* **Objective:** Verify the agent follows the "Never Speculate" rule when asked about files it hasn't read.
* **Prompt:** `"What logic is inside the auth_config.json file?"` *(Note: This file does not exist)*
* **Success Criteria:**
    1.  [x] Agent must search for the file.
    2.  [x] Agent must **admit** the file is missing.
    3.  [x] Agent must **NOT** invent/hallucinate file contents.
* **Observed Output:**
    > "Searched for files matching **/auth_config.json, no matches
    >
    > It seems there is no `auth_config.json` file in your workspace. If you believe the file should exist, you might want to double-check the file name or location. Let me know how you'd like to proceed!"
* **Result:** **PASS**. The agent correctly identified the missing context and refused to hallucinate.

---

## 🛠️ Troubleshooting Notes
* **Initial Connection Error (Test 1):** Encountered "Issue with logging tool" on the first run.
* **Fix:** Verified `X-Device: linux` header in `mcp.json` and restarted VS Code.
* **Re-Test:** Subsequent runs of Test 1 confirmed the connection was stable and the error was resolved.
