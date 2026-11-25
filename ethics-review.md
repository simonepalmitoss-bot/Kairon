ETHICS REVIEW


## Identification
The core ethical dimension centers on deploying a 24/7 AI virtual assistant 
directly into patient-pharmacist communication channels. 

* **Data Privacy:** The agent handles sensitive health data, such as images 
  of medication packaging and voice notes for chronic treatments under GDPR.
* **Clinical Safety:** Preventing misidentified drugs, incorrect dosage advice, 
  or medical hallucinations is a critical priority.
* **Transparency:** Patients must clearly understand they are interacting 
  with an automated agent rather than a human pharmacist.

## Analysis
Automating stock lookups resolves a major operational bottleneck in local pharmacies, 
saving up to two hours daily while cutting response times to under 20 seconds. 

However, severe harm could arise if the LLM misinterprets handwritten prescriptions 
or recommends drug substitutes without authorization. 

Balancing efficiency against clinical safety requires strict functional boundaries: 
automation strictly handles inventory lookups, never independent clinical judgment.

## Design Response
To mitigate these risks, the system implements structural safeguards:

* **Strict Guardrails:** System prompts prohibit medical advice and force 
  instant escalation to human pharmacists for clinical queries.
* **Privacy by Design:** AI is isolated from live pharmacy databases via 
  Google Sheets architecture, adhering directly to GDPR standards.
* **Oversight:** Multimodal input safety is supported by rate-limit retries 
  and automated daily activity summary reports.

## Honest Limitations
Despite system guardrails, processing damaged packaging, unclear voice notes, 
or handwritten text retains a non-zero error rate. 

Additionally, reliance on third-party LLM and messaging APIs introduces potential 
rate limits and service disruptions during peak traffic hours. 

Consequently, continuous human auditing of daily logs and active pharmacy 
supervision remain essential safeguards against automated failures.

[ethics-review.md](./ethics-review.md)
