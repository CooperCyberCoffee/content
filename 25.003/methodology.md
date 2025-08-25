# Methodology: AI STIX Extraction Challenge

## Research Question
Can current-generation AI models produce production-ready STIX 2.1 bundles from threat intelligence reports?

## Background
STIX (Structured Threat Information Expression) 2.1 is the industry standard for sharing threat intelligence. Every major threat intelligence platform expects valid STIX formatting for automated ingestion. If AI is to revolutionize threat intelligence, it must handle this fundamental requirement.

## Experimental Design

### Phase 1: Prompt Engineering
Each model was given an identical seed prompt:
> "Generate a prompt to do the following: Extract all valid STIX 2.1 objects from a CISA advisory in pdf format. Remove duplicate objects. Then create a valid STIX 2.1 bundle from the extracted STIX objects that can be used for sharing threat intelligence."

This approach allowed each model to optimize its own extraction methodology - testing both their understanding of the task and their execution capabilities.

### Phase 2: Threat Intelligence Extraction
Each model used its self-generated prompt to analyze:
- **Source:** CISA Advisory AA23-320A (Scattered Spider)
- **URL:** https://www.cisa.gov/news-events/cybersecurity-advisories/aa23-320a
- **Content:** 19 pages of threat actor TTPs, indicators, and mitigation strategies

### Phase 3: Validation
Two-stage validation process:
1. **JSON Validation:** Basic structural integrity check
2. **STIX 2.1 Validation:** Schema compliance using stix2-validator v3.2.0

## Models Tested

| Model | Version | Type | Provider |
|-------|---------|------|----------|
| Claude Opus | 4.1 | General Purpose | Anthropic |
| GPT-5 | Thinking Preview | Reasoning-Enhanced | OpenAI |
| Gemini | 2.5 Pro | General Purpose | Google |
| Copilot | Think Deeper | Reasoning-Enhanced | Microsoft |
| DeepHat | V1-7B | Security-Specialized | Local/Open Source |

## Reproducibility

All test materials are included in this repository:
- Seed prompts: `./seed-prompt.md`
- Model-generated prompts: `./llm-generated-prompts/`
- Raw outputs: `./outputs/raw/`
- Validation scripts: `./validation/`

## Tools Used
- **Stix2 Validator:** [STIX 2.1 Validator](https://pypi.org/project/stix2-validator/) v3.2.0
- **JSON Validation:** Python json.loads() standard library
- **PDF Source:** [CISA Advisory AA23-320A (July 2025)](https://www.cisa.gov/news-events/alerts/2025/07/29/cisa-and-partners-release-updated-advisory-scattered-spider-group)

---

*This research was conducted as part of the Cooper Cyber Coffee initiative - exploring practical AI applications in cybersecurity through hands-on experimentation.*
