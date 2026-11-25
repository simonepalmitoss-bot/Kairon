# Kairon
Kairon, the best pharmacy assistent for all customers.

PROJECT NAME AND ONE-SENTENCE DESCRIPTION
 Kairon is a virtual assistant, that helps pharmacies with the management of drug request,while also managing a waitlist for customers who search for unavailable medicines and helping pharmacists by sending them daily logs.

 THE PROBLEM BEING SOLVED
Customers constantly message pharmacies asking whether a medication is available. Without automation, staff can spend one to two hours a day manually checking stock and shortages—slowing down responses when patients need their medicines promptly. This assistant automates those repetitive checks and waitlist updates so customers get timely answers and staff stay focused on care.

HOW THE AGENT WORKS
Recipe: Telegram Message to Input Routing, AI Agent & Reply
The process starts when a customer sends a message to the pharmacy via Telegram trigger. Decision Point 1 (Input Routing): The system detects the message format through The message could be, routing audio through Download audio and An audio, text through A text, or images through Download image and An image. The message then reaches the AI Agent, which uses Analyze request (as its chat model) and Simple Memory to maintain conversational context. Step 1 (Local Stock Check): The agent first checks local pharmacy inventory using Simulating winfarm sheet. Decision Point 2: If the medicine is in stock locally, the agent generates a confirmation reply. Step 2 (National Shortage Check): If unavailable locally, the agent queries AIFA shortage sheet to check national shortage status. Step 3 (Waitlist): If the medicine is unavailable and the customer wants to be notified, the agent logs the request using Wait list. Step 4 (Output): The final response is sent back to the user on Telegram via Send reply.

Background Processes:

Refresh shortage list (Once a week): Triggered weekly (Once a week), the workflow searches for AIFA shortage updates (Searches the files), extracts the data (It downloads them), formats fields (Edit Fields), filters records (Filter), updates the registry (Update AIFA sheet), passes through Limiting the reading node, and reads saved requests (Read waitlist). A Code node matches waitlist items against current shortage data, and an If node (If the medicine) splits the flow: if available (true), it notifies the user via Telegram (is available, text client) and removes the entry from the sheet (delete from waitlist); if still unavailable (false), it routes to is still unavailable with no notification sent.

Send daily report (Every evening at 20:00): Triggered daily at 20:00 (Every evening at 20:00), it reads daily logs (Read daily logs), processes them in Code1, and sends a Telegram summary message (Send a text message).

DATA AND APIs
Google Sheets API (Live): Used as the core database layer across the entire solution to query local stock (Simulating winfarm sheet), check AIFA shortage data (AIFA shortage sheet), manage patient waitlist requests (Wait list, including post-notification deletion via delete from waitlist), write/update national registries (Update AIFA sheet), and extract execution logs (Read daily logs). Subject to Google Workspace API Terms of Service.

Telegram Bot API (Live): Handles all messaging channels (Telegram trigger), receives user inputs (text, audio, images), downloads attached files (Download audio, Download image), sends bot replies (Send reply), delivers restock notifications (is available, text client), and dispatches evening summary reports (Send a text message). Subject to Telegram's Terms of Service and Bot API policies.

Google Gemini API (Live): Serves as the multimodal AI engine powering the AI Agent (Analyze request), processing raw user audio (An audio), and analyzing image uploads (An image). Subject to Google Cloud API Terms of Service under a configured pay-as-you-go tier with safety guardrails.

AIFA Shortages Registry (Public Live Dataset): Public official dataset provided by the Italian Medicines Agency (AIFA). Periodically downloaded online (Searches the files, It downloads them), formatted (Edit Fields), and filtered (Filter) to track drug availability nationwide. Open public data subject to attribution.

KNOWN LIMITATIONS
Handling Complex Audio & Visual Inputs

Audio Noise & Dialects: High background noise, rapid speech, heavy regional dialects, or low-quality voice messages may lead to transcription/interpretation errors in the An audio multimodal processing node.

Illegible Prescriptions / Handwritten Text: Photo uploads (An image) containing bad lighting, low resolution, or handwritten doctor notes may cause Gemini to misinterpret product names or dosages.

Fuzzy Drug Matching & Name Variations

Brand vs. Generic vs. Typo: If a user searches for a commercial brand name (e.g., "Tachipirina") while the AIFA shortage dataset or Winfarm sheet registers it under its active ingredient ("Paracetamolo"), exact string matches may fail if the LLM doesn't map them correctly.

Dosage & Pack Size Ambiguity: A drug might be available in a 500mg box but short in 1000mg. Broad queries like "Is Augmentin available?" can result in false positives or negatives if dosage specifications are omiTted.

Rate Limits & API Dependencies

Telegram / Gemini / Google Sheets Limits: High concurrent traffic can trigger API rate limits (e.g., Google Sheets API quota bursts or Gemini token/RPM caps), leading to temporary workflow execution delays or timeouts.

AIFA Public File Format Changes: The weekly shortage update relies on fetching and parsing the official AIFA file structure (Searches the files -> It downloads them -> Edit Fields). If AIFA changes the column headers, file format, or URL structure, the parsing pipeline will break until updated manually.

WHAT BREAKS THE AGENT?
Invalid or Missing Telegram User Metadata

If a user hides their privacy settings or if an invalid chat_id is stored in the waitlist, sending restock notifications via is available, text client fails with a Telegram API Bad Request: chat not found error, halting downstream execution unless error handling (Continue On Fail) is explicitly enabled.

Unexpected Google Sheet Formats

Renaming headers, deleting columns, or introducing blank rows in the Simulating winfarm sheet, AIFA shortage sheet, or Wait list will cause spreadsheet lookup/write nodes (Read waitlist, delete from waitlist) to throw runtime errors.

Prompt Injection & Off-Topic Medical Queries

Users attempting prompt injection or asking for complex medical diagnoses/prescriptions are intended to be rejected by safety system instructions. However, adversarial inputs could attempt to bypass constraints unless strict guardrails are enforced.

SYSTEM ASSUMPTIONS
Clinical & Safety Boundaries

No Direct Medical Advice: The system assumes all clinical evaluation, dosage counseling, and prescription validations remain strictly human-led by the pharmacist; Kairon only automates operational stock and availability messaging.

Data Consistency & Inventory Sync

Mock vs. Real Inventory Sync: The local stock check assumes the Simulating winfarm sheet accurately reflects real-time physical pharmacy stock without delays from in-person OTC sales.

Weekly Shortage Frequency: The system assumes a weekly sync cycle (Once a week) is sufficient for AIFA national shortage tracking.


ETHICS
