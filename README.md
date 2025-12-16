# 🕵️‍♂️ GitHub Typo Hunter (n8n Workflow)

**GitHub Typo Hunter** is an automated workflow built on **n8n** that scouts for random open-source repositories on GitHub, analyzes their documentation (README.md) for spelling and grammatical errors using **Google Gemini AI**, and delivers a consolidated report via email.

It is designed to help developers contribute to open source by identifying "low-hanging fruit" fixes (typos) in active Python projects.

---

## 🚀 Features

* **Randomized Discovery**: Uses a randomization logic to fetch a different set of repositories every time, avoiding duplicate reports.
* **Smart Filtering**: Targets specific criteria to find relevant projects:
    * Language: Python
    * License: MIT (True Open Source)
    * Status: Active (not archived)
    * Size: Mid-sized projects (50 - 1000 stars)
* **AI-Powered Analysis**: Utilizes **Google Gemini 1.5 Flash** to intelligently detect typos while ignoring code blocks, variable names, and technical slang.
* **Rate Limit Handling**: Includes wait steps and retry logic to respect API rate limits (GitHub & Google AI).
* **Consolidated Reporting**: Aggregates findings from multiple repositories into a single, clean HTML email report.

---

## 🛠️ Architecture

The workflow follows this logic:

1.  **Trigger**: Runs manually or on a weekly schedule.
2.  **Random Seed**: Generates a random page number (1-50) to diversify search results.
3.  **GitHub Search**: Queries the GitHub API for repositories matching the criteria.
4.  **Loop (Batching)**: Processes 10 repositories one by one.
    * *Fetch*: Downloads the `README.md` content.
    * *Decode*: Converts Base64 content to UTF-8.
    * *Analyze*: Sends text to **Gemini 1.5 Flash** with a strict JSON system prompt.
    * *Parse*: Extracts structured error data.
5.  **Aggregate**: Combines all individual repo reports into one summary.
6.  **Notify**: Sends the final report via Gmail.

---

## 📋 Prerequisites

To run this workflow, you need:

1.  An instance of **[n8n](https://n8n.io/)** (Cloud or Self-hosted).
2.  **GitHub Account** & Personal Access Token.
3.  **Google AI Studio API Key** (for Gemini).
4.  **Google Account** (for Gmail integration).

---

## ⚙️ Setup & Configuration

### 1. Credentials
Set up the following credentials in your n8n instance:
* `GitHub API`
* `Google PaLM / Gemini API`
* `Gmail OAuth2`

### 2. Search Logic (Customization)
The workflow uses the GitHub Search API. You can customize the query in the **HTTP Request** node:
`q=language:python is:public archived:false stars:50..1000 license:mit`

* *Want newer repos?* Add `pushed:>2025-01-01`.
* *Want specific topics?* Add `topic:machine-learning`.

### 3. The AI Prompt
The system uses a strict prompt to ensure JSON output. If you change the model, ensure the prompt enforces this structure:
```json
{
  "has_errors": boolean,
  "report_html": "<li>Error -> Correction</li>"
}
```
### 4 🚧 Handling Rate Limits
If you encounter 429 Resource Exhausted errors from Gemini:

* Increase the Wait node duration inside the loop (default: 5 seconds).
* Ensure the Retry on Fail option is enabled in the LLM Chain node settings.

### 📝 License
This project is licensed under the MIT License. Feel free to fork, modify, and use it to improve open-source documentation!

Automated with ❤️ using n8n and Gemini
