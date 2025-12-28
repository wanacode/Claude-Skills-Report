# Comprehensive Investigation into Claude Skills: Architecture, Use Cases, and Implementation

**Key Points**
*   **Definition:** Claude Skills are modular, reusable packages of instructions, scripts, and resources defined by a `SKILL.md` file that allow Anthropic’s Claude models to perform specialized, repeatable workflows [cite: 1, 2].
*   **Core Architecture:** They utilize **progressive disclosure**, a mechanism where Claude only loads the full context of a skill when its metadata description matches the user's intent, thereby preserving context window space and reducing token costs [cite: 3, 4].
*   **Differentiation:** Unlike **Claude Projects** (which provide static, always-on context) or **MCP** (which connects to external tools/data), Skills define *how* to execute a process or workflow [cite: 5, 6, 7].
*   **Versatility:** Use cases range from administrative automation (e.g., \"Meeting Whisperer\" for summaries and Jira tickets) to complex software engineering (e.g., \"Superpowers\" for architectural planning) and brand compliance [cite: 8, 9].
*   **Open Standard:** The architecture is designed to be portable, with potential compatibility across different environments like VS Code and GitHub Copilot, moving beyond a proprietary feature to an open agentic standard [cite: 10, 11].

---

## 1. Introduction

The evolution of Large Language Models (LLMs) has shifted from simple conversational interfaces to agentic workflows capable of executing complex, multi-step tasks. A significant advancement in this domain is the introduction of **Claude Skills** (often referred to as Agent Skills). Released by Anthropic, Skills represent a paradigm shift in how users interact with AI agents, moving from ephemeral prompt engineering to persistent, version-controlled capability definitions.

Claude Skills are essentially folders containing structured instructions (typically in a `SKILL.md` file), metadata, and optional executable resources that teach Claude how to perform specific tasks consistently [cite: 12, 13]. Unlike standard prompting, where instructions must be repeated or pasted into every session, Skills are persistent \"knowledge packages\" that the model can dynamically access when relevant.

The significance of this technology lies in its architectural approach to **context management**. By utilizing a technique known as \"progressive disclosure,\" Claude Skills solve the \"context pollution\" problem—where overloading an LLM with too many instructions degrades performance—by only loading detailed instructions when a specific trigger condition is met [cite: 3, 6]. This report investigates the technical foundations, diverse use cases, and implementation strategies of Claude Skills, providing a comprehensive resource for understanding this agentic capability.

---

## 2. Technical Architecture and Mechanics

To understand the utility of Claude Skills, one must first understand the underlying architecture that distinguishes them from standard custom instructions or file uploads.

### 2.1 The `SKILL.md` Standard
At the core of every Skill is the `SKILL.md` file. This file serves as the entry point and definition for the capability. It combines structured metadata with natural language instructions.

*   **YAML Frontmatter:** The file begins with a YAML block containing essential metadata. This must include a `name` (lowercase, alphanumeric, hyphens only) and a `description` [cite: 14, 15].
*   **The Description Field:** This is the most critical component for discovery. Claude does not read the entire skill file initially; it scans the `description` field to determine relevance to the user's current prompt. If a match is found, the full skill is loaded [cite: 14, 16].
*   **Markdown Body:** Following the frontmatter, the file contains standard Markdown instructions detailing the workflow, examples, and edge case handling [cite: 17].

**Example Structure of `SKILL.md`:**
```markdown
---
name: brand-voice-bodyguard
description: Enforce company tone and style guidelines on drafted content. Use when the user asks to review, edit, or draft marketing materials.
---
# Brand Voice Guidelines

## Core Principles
1. Active Voice: Always use active voice.
2. No Jargon: Avoid corporate buzzwords like \"synergy.\"

## Workflow
1. Analyze the input text.
2. Identify violations of the style guide.
3. Rewrite the content preserving the original meaning.
```
*[Based on citations 30, 32, 50]*

### 2.2 Progressive Disclosure
The defining architectural feature of Claude Skills is **progressive disclosure**. In traditional LLM interactions, providing extensive documentation or complex standard operating procedures (SOPs) consumes a significant portion of the context window (the limit on how much text the model can process at once). This \"context bloat\" increases costs and latency while often confusing the model [cite: 3, 18].

**How Progressive Disclosure Works in Skills:**
1.  **Discovery Layer:** At the start of a session, Claude only loads the metadata (specifically the `name` and `description`) of all available skills. This consumes very few tokens (approx. 100 tokens per skill) [cite: 19].
2.  **Intent Matching:** When a user issues a query (e.g., \"Summarize this meeting\"), Claude evaluates the descriptions of available skills.
3.  **Dynamic Loading:** If the \"Meeting Whisperer\" skill description matches the intent, Claude loads the full `SKILL.md` content and any associated resources into the active context [cite: 20, 21].
4.  **Execution:** The model executes the task using the now-loaded specific instructions.

This architecture allows an agent to have access to hundreds of specialized skills while keeping the initial context window lightweight and efficient [cite: 4, 22].

### 2.3 Directory Structure and Resources
A Skill is not limited to a single text file. It is a directory structure that can contain:
*   **`SKILL.md`:** The primary instruction file.
*   **`resources/`:** A subdirectory for static files (e.g., PDF templates, extensive style guides, CSV data) that are referenced by the skill but not loaded until needed [cite: 20, 23].
*   **`scripts/`:** Executable code (Python, Bash) that the skill can trigger if the environment supports code execution (e.g., Claude Code or the API with code execution enabled) [cite: 15, 24].

---

## 3. Comparative Analysis: Skills vs. Other Architectures

Confusion often arises regarding how Skills differ from other Anthropic features like Projects or the Model Context Protocol (MCP).

### 3.1 Skills vs. Claude Projects
While both features allow for customization, they serve different architectural purposes.

| Feature | **Claude Projects** | **Claude Skills** |
| :--- | :--- | :--- |
| **Primary Function** | Context & Knowledge Base | Workflow & Action |
| **Loading Behavior** | **Static:** All files and instructions in a Project are loaded into the context immediately and persist throughout the chat [cite: 5, 25]. | **Dynamic:** Only metadata is loaded initially. Full instructions are loaded only when triggered by user intent [cite: 3, 25]. |
| **Use Case** | \"What we are working on\" (e.g., a specific software project, a novel draft) [cite: 6]. | \"How to do a task\" (e.g., how to run a unit test, how to format a memo) [cite: 6]. |
| **Scope** | Project-specific. | Cross-project; can be used in any chat or environment [cite: 1, 25]. |

**Analogy:** A **Project** is like a shared office containing all the files for a specific client. A **Skill** is a specialized employee (e.g., a graphic designer) who enters the office only when that specific expertise is required [cite: 7].

### 3.2 Skills vs. Model Context Protocol (MCP)
The Model Context Protocol (MCP) is a standard for connecting AI assistants to systems (databases, tools, repositories).

*   **MCP:** Provides the **tools** and **data**. It connects Claude to GitHub, Slack, or a PostgreSQL database [cite: 23].
*   **Skills:** Provide the **instruction** and **procedure**. A Skill teaches Claude *how* to use the MCP tools to achieve a specific outcome [cite: 5].

**Example:** An MCP server might give Claude the ability to \"create a Jira ticket.\" A \"Bug Reporting Skill\" would contain the instructions: \"When creating a Jira ticket, always include reproduction steps, severity level, and assign it to the QA lead.\" The Skill orchestrates the MCP tool [cite: 5].

---

## 4. Comprehensive Use Case Analysis

The versatility of Claude Skills allows them to be applied across various domains. The following sections detail specific use cases identified in the research.

### 4.1 Enterprise and Administrative Automation

#### 4.1.1 The Meeting Whisperer
This is one of the most cited use cases for Skills, designed to transform raw meeting transcripts into structured business artifacts.
*   **Function:** The skill ingests a transcript, identifies key decisions, assigns owners to action items, and drafts follow-up emails [cite: 8].
*   **Advanced Implementation:**
    *   **Jira Integration:** More advanced versions of this skill can interface with MCP to automatically create Jira tickets for identified tasks [cite: 26, 27].
    *   **Sentiment Analysis:** It can flag emotionally charged moments or critical disagreements in the transcript [cite: 27].
    *   **Matrix Generation:** Creates a task matrix with columns for \"Decision,\" \"Owner,\" and \"Due Date\" [cite: 27].

#### 4.1.2 The Inbox Triage
*   **Function:** Acts as an executive assistant for email management.
*   **Workflow:** The skill categorizes incoming emails based on urgency and sender (e.g., \"VIP,\" \"Finance,\" \"Newsletter\"). It then drafts replies using a specific tone (e.g., \"polite decline\") [cite: 8].
*   **Safety:** Best practices suggest the skill should draft replies for review rather than auto-sending, particularly in the early stages of deployment [cite: 8].

#### 4.1.3 The Brand Voice Bodyguard
*   **Function:** Ensures all generated content adheres to strict corporate identity guidelines.
*   **Implementation:** The skill folder contains a `resources/` directory with the official style guide and a list of \"banned words\" (e.g., \"synergy,\" \"innovative\").
*   **Trigger:** When the user asks to draft a blog post or press release, the skill activates, checking the output against the style guide and rewriting violations [cite: 8].

### 4.2 Software Engineering and DevOps (Claude Code)

In the context of **Claude Code** (a terminal-based agentic environment), Skills take on a more technical role, often executing scripts and managing complex development workflows.

#### 4.2.1 Superpowers (Brainstorm/Plan/Execute)
This is a meta-skill set often used by developers to structure the coding process.
*   **`/brainstorm`:** Analyzes the request and lists necessary components (frontend, backend, routes) [cite: 9].
*   **`/write-plan`:** Generates a detailed implementation plan, including dependency graphs and file paths, saved to a markdown file [cite: 9, 28].
*   **`/execute-plan`:** Orchestrates the execution, potentially spinning up sub-agents to handle different parts of the codebase simultaneously [cite: 28, 29].

#### 4.2.2 Webapp Testing (Playwright Automation)
*   **Function:** Automates Quality Assurance (QA) by writing and running browser tests.
*   **Workflow:** The user says \"Test the login flow.\" The skill uses Playwright (a testing framework) to generate a script that opens a browser, inputs credentials, and asserts success. It then runs the script and reports results [cite: 9, 29].
*   **Benefit:** Allows developers to run end-to-end tests via natural language without manually writing the boilerplate test code.

#### 4.2.3 Rube MCP Connector
*   **Function:** Solves the fragmentation of external tools.
*   **Workflow:** Instead of setting up individual authentications for GitHub, Slack, and Notion, this skill connects Claude to a unified server that manages these integrations, streamlining automation workflows [cite: 9, 29].

#### 4.2.4 Error Handling Specialist
*   **Function:** Improves debugging by enforcing a specific philosophy on error analysis.
*   **Workflow:** When an error occurs, this skill categorizes the error type, provides language-specific advice (e.g., Python vs. Rust), and guides the model to find the root cause rather than applying random fixes [cite: 30].

### 4.3 Document and Data Management

#### 4.3.1 Document Suite (Official Anthropic Skill)
*   **Function:** Enhances Claude's ability to create professional documents.
*   **Capabilities:** Unlike standard text generation, this skill can generate formatted Word documents (`.docx`), Excel spreadsheets with formulas (`.xlsx`), and PowerPoint presentations (`.pptx`) [cite: 13, 29].
*   **Integration:** This is often used in conjunction with Box AI or other storage solutions to automate file creation directly within enterprise storage systems [cite: 22].

#### 4.3.2 Standardized Report Generator
*   **Function:** Converts raw data into polished reports.
*   **Workflow:** The user uploads a CSV or dataset. The skill ingests the data, identifies trends/deltas, and drafts a report following a specific template (e.g., \"Executive Summary,\" \"Key Metrics,\" \"Risks\") [cite: 8, 31].

### 4.4 Creative and Niche Use Cases

#### 4.4.1 Algorithmic Art Generator
*   **Function:** Generates visual art code based on descriptive prompts.
*   **Workflow:** The user describes a visual (e.g., \"Blue-purple gradient flow field\"). The skill generates `p5.js` code to render the artwork, ensuring reproducibility via seed control [cite: 29].

#### 4.4.2 Slack GIF Creator
*   **Function:** Creates custom animated GIFs optimized for Slack.
*   **Workflow:** The user describes a scene or reaction. The skill generates the frames and compiles them into a GIF format suitable for workplace communication [cite: 29].

---

## 5. Implementation Guide: Creating a Custom Skill

Creating a Claude Skill involves setting up a specific directory structure and writing the metadata and instructions.

### 5.1 Step-by-Step Creation

1.  **Create the Directory:**
    Create a folder named after the skill (e.g., `my-custom-skill`). The name should be kebab-case (lowercase with hyphens) [cite: 15, 31].

2.  **Create `SKILL.md`:**
    Inside the folder, create a file named exactly `SKILL.md`.

3.  **Define Metadata (Frontmatter):**
    At the top of `SKILL.md`, add the YAML block.
    ```yaml
    ---
    name: my-custom-skill
    description: A concise description (max 1024 chars) of what the skill does and when Claude should use it.
    ---
    ```
    *Note: The `name` must be lowercase, alphanumeric, and hyphens only. No spaces or underscores.* [cite: 14, 15].

4.  **Write Instructions:**
    Below the frontmatter, write the instructions in Markdown.
    ```markdown
    # My Custom Skill Instructions
    
    ## Overview
    This skill helps the user perform X by doing Y.
    
    ## Steps
    1. Step one...
    2. Step two...
    
    ## Examples
    User: \"Do X\"
    Assistant: \"Here is the result of X...\"
    ```
    [cite: 17, 32].

5.  **Add Resources (Optional):**
    Create a `resources/` folder for templates or reference files. Reference them in `SKILL.md` using relative paths (e.g., \"Refer to `resources/template.txt` for formatting\") [cite: 20].

6.  **Installation:**
    *   **Claude Desktop/Web:** Zip the folder and upload it via Settings > Capabilities > Skills [cite: 23, 33].
    *   **Claude Code (CLI):** Place the folder in `~/.claude/skills/` or the project-specific `.claude/skills/` directory [cite: 34, 35].

### 5.2 Best Practices for Skill Development

*   **Specific Descriptions:** The `description` field is the \"trigger.\" It must be distinct and keyword-rich so Claude knows exactly when to activate it. Vague descriptions lead to skills not loading [cite: 14, 15].
*   **Progressive Disclosure:** Do not put everything in `SKILL.md` if the file is large (>500 lines). Move detailed references to the `resources/` folder to save tokens when the skill is loaded but the specific reference isn't immediately needed [cite: 14, 18].
*   **Atomic Design:** Create small, focused skills (e.g., \"Python Error Handler\") rather than monolithic \"Do Everything\" skills. This improves discovery accuracy [cite: 17].
*   **Security:** When using skills that execute code, use the `allowed-tools` metadata field (if supported in the environment) to restrict access (e.g., allow `read` but deny `bash` execution) [cite: 15, 34].

---

## 6. Future Implications and Ecosystem

### 6.1 Open Standards and Portability
While originating with Anthropic, the \"Agent Skill\" architecture (folder + `SKILL.md`) is being positioned as an open standard.
*   **GitHub Copilot Compatibility:** Research indicates that this structure can be adapted for GitHub Copilot by placing skills in `.github/skills`. This suggests a future where a single skill definition works across multiple AI assistants (Claude, Copilot, etc.) [cite: 10, 11].
*   **Cross-Platform:** The separation of \"discovery\" (metadata) and \"instruction\" (markdown) makes the format portable across different agentic frameworks [cite: 16].

### 6.2 The \"Skill Creator\" Skill
Anthropic provides a meta-skill called \"Skill Creator.\" This is a skill designed to build other skills.
*   **Function:** It interviews the user about their workflow, generates the necessary folder structure, formats the YAML frontmatter correctly, and bundles resources.
*   **Significance:** This lowers the barrier to entry, allowing non-technical users to create complex agentic workflows without manually editing Markdown or YAML files [cite: 12, 32].

---

## 7. Conclusion

Claude Skills represent a maturation of AI interaction, moving from ephemeral chats to structured, repeatable engineering. By leveraging **progressive disclosure**, Skills solve the critical challenge of context management, allowing agents to possess a vast library of capabilities without the cognitive load (and cost) of constantly processing them.

From the **\"Meeting Whisperer\"** streamlining corporate administration to **\"Superpowers\"** orchestrating complex software development, the use cases demonstrate that Skills are not just prompt templates but functional software components. As the standard evolves and potentially integrates with other platforms like GitHub Copilot, Skills are likely to become the fundamental building block of personalized AI agents in both enterprise and development environments.

### Summary of Key Resources
*   **Official Repository:** `anthropics/skills` (GitHub) [cite: 13].
*   **Community Lists:** `awesome-claude-skills` [cite: 35, 36].
*   **Documentation:** Anthropic System Guides on Agent Skills [cite: 14, 37].

**Sources:**
1. [claude.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQEvosKIvifuKHrj3PZCBfJzQ5yXWWhejlDh_EDTe_A21lMrSUXmXYo8uGSmQUMTuk4tIKFFXe3GXWtVVT1NBzG0dY7sYlkgur2D5ykkFovq2qwh1Pnmk1jrGbfRVOq3H9zwRzAXsvm7OJEQD1vVpyWEMO8X5bQ=)
2. [skywork.ai](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQHJoYdB-3_H89hWQAPj4yo98QD54IgDo_ENAmg3PQ59-UyJESNzptVSRlrVmIa8x9dyMWsSatvFLoIWKSOjNXZ0LYTGfXgDtI_WXlNBM2ADotQLsOsuagt8-i3_gSrz9eNaMe7jV25QODvNZ3THPGuoVraC0-Rp9queLOMnWHU=)
3. [eesel.ai](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQEcEDRv7qFJUBhbZqerWuwF-J56ezBxmC8ZTJkGXDLwvLtYffucrqIo-D7ODkeC7gej7rQucq_Prqrtp5i9Se0IPtlBbkCfSoac2o0gZXXHpqmmyma1t5DSDQWezJk=)
4. [medium.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQFw7JAH3y6LWFlhOmlV0gNKFLgZ3VAMDcyb5rcT7K3UbwIKgUNG8ZpJC5S6IjV0xBf0lhVTJkGfnujl5m8Q2LKcnJwoWkcWYIRjuyUFHBrnophw_MAFVMEdR5elYobmttxEz1Gwf5JYegLjQsg5ZayLuFjU8jggeE-M54o1CWR_lKW3DQZc_WktuchuGxUa3Dgn6lZmebzRKlSAHtG2FAAurK-B9-IYEZX0dVg=)
5. [reddit.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQEoiRSf7DSpQX7T9JPMkaZLQUgi4wztCQxHAxWwdboossTARPyk0pGFzJ2N5Zqzjynown5OiiwS3eDtWMiZynjnXCHYuEhjUtBpPb69vjiDdBwUDwDrlnm-kQa2TPifUUf2-rRJrSh7H6ynqpCu77cK7kr8j3u2z2Ttiv4r0fQLQl9JXIPSWm78sIxRZH5X36wMokXQU2hhzrK43w==)
6. [thedesignsystem.guide](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQEQlr4GV9XWIVsteDbrCzu5_BUH1PUo0sJyTVp1azYMKYsQxfkqSODsnO8tahjWf9tB95hHRmvWd4pAjwMI21WyGfqEINla92i3YTGN0pLwOcaD0FqU7-_MXSow4HsVpSmqneEqsigeaczyaYK3u-26Ktmr1GcR5ppyA_rZ)
7. [towardsai.net](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQEyCSy2-PKSHQWYBDYInNC_wYwMAm6QRx_JvnEnV8NvIbw9nFFCZgIulKnlHO0ipCmH9kcQ-PWyMdToC3AadkLPmXuFZmp9a9FjjhIC3nzbkmbp8Ka57yYcXaAkRqlE1ifIUrTDO6DG3whs9cKwLuronty0ahygkFKzd62n-DaVct2lwQ3XB_LrLww_9L2ittCLarRl0uyDb2bjNVukjw==)
8. [sider.ai](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQGUPpW2jp7mRsTUgkXS19uSni77PmV-4asMfU6fidd3LbBu62EUhxZKPYDHtLsqJkeAg_7oeYcxU706vxCcutdOK_P_S91uWUUDql_sCmTl2snB6TKQoedM-iPXCVclBwx_HlpITxDOZGhUCaG9kzh94gTpqp4uPTegJArw0M0-ErHPyXkMB6TRM4dsUPUi45Oc4ByTHK5-xbrN2eEZ0WPdqdUo6t8=)
9. [apidog.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQE-cwcb3W3R-2qUidR4lEjbCOSQCKlgD4LiOXD2h98BPwR2j1pU45_H-HqFnAZpaUl11zKfXshOVU4QvLEEbfgQp0ungoOGpt0Gg0oIodeq4uyeKfoP6-bCmogTH0UfsRdWF6O8FENarQ==)
10. [cloudnativedeepdive.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQG_ksFZa9IJBpdjzaPBsH1Ywf4oXckqHAqVP0J9h7TLcDHe_aYnqG6fMYOzNTDnJWy9i-mzZVAWGFIOfq7X9D5HmWosKYzhMGBUCgyXzw-c3i_9QdK_BNi6N_uk7uMVX33HjjrdFwvJ4u_sp0P78C0q09ejHLdKP70=)
11. [visualstudio.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQEBOkRyqEjgkd8Vo8ofnGuH-uzOiddMyuny7GWUoYUotgdVXhKbMw3Z4lQiIvWMi_OgxXzIBn3PW9mAChz2BlmRdmFybI4arGH7eMnzQGHtdNRlXcxStNcwYXmFdxzvpqG9VH-tu4NAndhdGOiy78E14-s_AMvMZHZs-Jc=)
12. [claude.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQGVca5eGD3qlvhbUNX5-wJ3U-JuRoacQEZZzGeM152DQQ31rOA8t8JM-5xJ3vO5iLUC7WtoIh8KM7z4V1lJJfpy637i2Pg9B9x_umI-HCjNaRyszyE=)
13. [github.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQFRnvirT9e6z_FUnwKNSKqsRMG_XmazPcB1VzgkDVr-im80Sqs64GJrTTl-zPEf_qpiki0BYsFDqX2F_ywKCA88ng34IE9O-uJ-XELg6My2iJPDIzEEMbTQr70=)
14. [claude.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQHHdREi8qQh4RzkREVrRSk2-bdHHehyfc_OdqUou3Em_2orq5_dUL8t1r90SvAJy0ozCg67cKAGO7yoHJ_JSYgGHLIYmz9yrUmNs33QF2hmBEIws3s5qbJAYoJgUSRhYvD5pNz64UtUmu2fQsS-_6Z1XMVF0Zqd1aeXwPMK4yxw5lTcloi4Ww==)
15. [medium.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQGmVqc5ROO7vpYAfkTgjQmYqyquFVtXfsG3aeguLve-wuj3_dmt_Co03dYtM9JAQQCOYX0pXnY2ppQumVSyZvU2C-v7be-AXzC57sBk7KFH-u_UFtPlIifvemnva_qg8JR6R1FGCXOVx-RdfdiubvuIZnK8e2WuWmZxMPokkIOXj30lJn3BI-eSkOXyuFOQ7A1q5vvklv8p6MAVqD5JAqOUQsC0th51Pg==)
16. [github.io](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQFozpFETvadCzzuDXmuv83bFLJTg4uB7n86JRqlbYcDgVchpS05OdsCIqis9jc3sADAnhTTEt4ZqJvbr6IrcABjpqir2mPOF_B0_luWiEtkASOky67GePxYzRmVHmkNh19gnCCq7nyF1K44LSWQKMgCG3o1Lmwa1HnjKdYV-5a50FbRH_yvKNjqOMYnVecd5AsicOJvotc=)
17. [claude.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQHo6mnqDOXLHPsxaKNvc9QkMJS9Sv53rvo38xnPSwlxcK3VqNrxuyv5YvpZh5rzMgwBE5_IQUMVpGszoEY3tJ2QhrDEzbGd-EYZwCIHg6Ppw3LBubnA6vVfco_fiqign261taXL98n7E0Osja7aLjTH7VI0hV04Mza0kvbLMuul15k=)
18. [claude-plugins.dev](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQF5xlhwQ_x23bo1_7VCGVChcBDAqzeJMtw9m50rG36F04Ym6nVEdTGp3A0nF0K63UT-dclv6OETpY39mYbbywan3dCFwftbAlsqFQCe6IYcm-wXLAwgskAi5TyDq3N3XJtw_CL1a-2j8du5gted_FrVfyXwGy_qaRQAU1wfpNCYRM1cjg==)
19. [medium.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQHjxTWX-SovV4BnCuFTLEfIUILQEHg2mKYgIatZyFwZHvkkoB8r8cUxVVxJuRgMjKBer0HTMkG6nZxxb5Uslbkfm7hfeWuRmTbZMertzVOUY2Tqv6KFf2yzH84e1WGjH_rC1QBfiyQPQ4gmELBQIhEmzUg=)
20. [skywork.ai](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQHjb_cSgUsw5g7HoT2TeBG516vrhpSZE-PGYf8ISQfAX7aTGD4_5x8F1u61RxrwliQBmyPYYcJ--ullflpZfp-YO8b4VuhyacoxGgWyqpWfeiIlkskiw__VsQS-uLyZJLWJb0FPQ5qh22tie6a5bJj4HxKJULOob2EYZb_dJtmILeWlLCeavGXO)
21. [substack.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQFQJT0OsiKae2lL3rYvLkoGGiyP2neQh2tVuwEbWYwECHaNyZmZDiulryqk-LYgSOzr0q4Pr2ddXy2lFIXdJyrijVgqDzYpl_klbz-bVA33MmP75o3vZmHieT8eg9QpzvqdfJBkYtQiWHglmngyXN9nxI9NtBV291SuViCwGyQnIF87nQ==)
22. [box.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQFfjNKSQ9T_kViyUJJ7_QapGVD2db3JwqwwuYkhPM9EFX6W4O-moiIRqEvCrh6_JYFWkHH1DTOozpRgFpT3iW3qqHdRQIXWXGpbCN3jMgUAAShRboz_hkuQ5_AQT8SVekJZdtRq4zLm9iR6NPco0riehecj4BnImnYGjhgtsXU-kC3C8gmCJeQlbA==)
23. [codecademy.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQFAHAoGKVYDmY-K6sK47rzzSob60_d7cUttmp3eB0MneeiGEotssyB1WGmHt5asdGE64Ltyxt_8YIO5A9HBm9PG0CXbUXww-uVwZcyLbrnwJkcCtLlIXPpK6dHIEjdqrrOtCLv9epB03_cTqaQrkhaZxE-9)
24. [github.io](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQHq5Nb_v6eSiaJROA_cMNjZdkX431W3g4S2auJVavBhWhKGf3SQW979jGbLPfKeBaagsew6O9uEsdqdHn_EVONMdM1JSkhP6kDdaaCwE_od8qM2ql6h41pYXN9xjy1ihUZ9HWRszKIMx68QHMYEq_wD1R3alUISrQ-fc8EmVA==)
25. [youtube.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQHIcKwuBtPyWsy0UmTnhOKfClatHGBv8babzv66_rVCrwGrsdzdZ06EgV7Vimr8qPme5FpISHG6CdCpKWA1tEeJpGqJ8Xxb9MopmxTuTzQmJgKJD7SM0RR-AoUEsK0N)
26. [prompttoproducthub.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQHh_v16QX1cYa-MhEbWdIshOyfiaPHgy0_UYtJutrh4PwKAaU8ctqnAA9CqBmjj0wDvnFH-fA4_WDGYBy_oOHW6WUkjabhUdWlIaP8NE1YB-Ps8aLDqOyCwRelNzxLAPCVkeXJIcYhQL3jM7ZJ6Rc2AJO6tfQS7nShBahcUfttUQ-6AWNIq-DYoT55fZY3rqc92nw2QsIHnbBJb7PyAryOQyD1k3NjxNYJwAFKZbh2-s7sLHmpa7hTXIiQ26tbP1g==)
27. [prompttoproducthub.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQHU1T8cqizcHLfAv4Lk6fSY7pTweGvGSwefRMEeaNP9pfIcHssNIw7_bTli55s59VIbaiaozcSZq_kAkH9wz18bqB1sdf1Emht9Kj8WctVjFbb_ym-2kGZqNZSjM7RshW4lrttx7t3-KjYskkEAnyLsznJHMF9fU81wD2RL7j815KEhZDctizeTkrzUyX5lfANsal-QvFn_ZIZvoXy4YEgcI9SfWClB4-wc5D2oRpg=)
28. [medium.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQFJI8JKzZa9sJVV-r5n750Fw045hy9N1smJaWBJe_rrF8SqVwn_XZAO0Qk6Yyvf8c2QQEoQ2dVsAeNe8DzAkZXvfdSTFqp5VIbEsSke00V3Y6vKQdgbJeB8B4u5JMbrtj4KV1KsGLSYtacxDRdYOABGpPHPap3aZwCHeJZ90xNzM2QcqM-4013xzd_BwDlEn6U9XW2kVu0=)
29. [reddit.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQFDGKUI_Cd7SFhNyW-vNc3aUV1V5-oUaA1yVjwK674mMfogwZn1gBkDADfbqqPuQBDKhu-kkYjCWpwMUws47AcbEBNxNdJnU9iUdMqgS7wtkoIE9Cbs79Up911HxSe52TvWBkNQjKKc7MSWsQWurwZQhrYb97o8OhMO3_AhBhRBeMZfznfmrzUjyoEYBcXAsY4oh97T23AcP78BlQ==)
30. [youtube.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQEXHusfAT1zP8-FBaLZJ7mqOc9zB44F-_JiMqPjrHXZSHB4bk_4xLw6hoxa92hIkI3lPYZmFwrZuslHXw0IqdRFTQL-Ax1B5GxqFLiLIJ_TCoav-8WW5R2NqG0Yex93ptw0)
31. [skywork.ai](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQHr5UDRgDOmUOdMWTrTA6KC8qYLUJFndZnNYJd6NdbjxSyA4zvqv54fqe9vDH6nHXzLXZrYHDI5u7-V8wW55EGz8E_c9OEg4KFsMgEqTWJmDrb4tsgymQ1zh6tHoferXpe_3LVsyfapCP2dr-dSQXbcPipD2CoeiuB2FFgzyMQ3597jrAgD)
32. [claude-plugins.dev](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQGuN08DTlG3IRhEzbj_wsiNvkspoGPWuxdUjDcyczaAhv44bVIrLNgr_l69q5slE5LvVqRHQR_RGahzcoofhhL4QFnqwLr8AnykWkWZcteOrqjyAelj3tPtvyfONqaAzCv_wLQ6lMpyaREHNepYJZihsxc=)
33. [claude.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQE3VK7lIrFkV25b2AK7fpJLCfvgYQ0q8AsV31H9G5Oz9zWc9vTiCZPqsFBuyxSZiWIp0qSEsRhz5rUlRnUW3LiKoZMVx9_KDtMWqaYwSBZxasI3zTcraQBvyf1uFpextqoDbpsIla0SgjBay-wpmobJtrXBnO6p6ClkaR05)
34. [claude.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQH4cZlE0q--7x3Oe2bYHqjTNtxBGM_xjhxnTh58OawRMB9-Tb0JiORghp1d77-d56PUQRdP4vnwK0rRKzpUkqq4B62NGzQvP9G8zZ-8_OHmm9ag4IXyjBPOFe-rJQ==)
35. [github.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQH73A5ykhCxN1cek78Je3B7xlp3bDKTInJ8ERg9VRuJAbJPuwrlfXfjYW7dbqFCvJQ1eWw4eHLo01v67DHG5f2cfyWfoVDJfhfEfmSWUZlhgJfIHswtiM8_MkmUYxGaiRHyCl322upatxw=)
36. [github.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQFQ3oSQ9R2lmBIpzpDhrSl2Tg-k2bE9aOdXjAnJ_few6yDqTATNGERkOmJPR6DEvHiVNLRiJNS8VzQW-NPnC6lpmPp3tf8bCgBSvQZQ9d1m52FcseL_1Ds8BjNWEXJHaxFrUmgR0ipD)
37. [claude.com](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQHt3boE2pRexJNIhpe7erBRKxvKnJVtvJAgmW2r49rGXxWBlDtXAfzAevG3F8GMN2hAzgcZxlBb_LMZhG5q_k2hOE189rc6vAgSwJc13gx6yE74KnDczKu-gugFzP0CuSLr5b_PVsVhpO85wsiGP9bdRRbWWkMRe0ekjKyH)
