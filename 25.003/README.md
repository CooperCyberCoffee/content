# LinkedIn Post - STIX Extraction Results

**Tested 5 leading AI models on extracting threat intelligence from CISA advisories. None produced valid STIX bundles. Here's what that means for your security automation.**

I gave each model the same task: Extract threat intelligence from CISA's Scattered Spider advisory (AA23-320A) and format it as STIX 2.1 for automated sharing.

This is a routine task security teams need daily. The results reveal critical gaps in AI readiness for production security operations.

**The Results:**

✗ Claude Opus 4.1 - Valid JSON, failed STIX validation (35+ errors)\
✗ Copilot Think Deeper - Valid JSON after timeout/retry, failed STIX (150+ errors)\
✗ Gemini 2.5 Pro - Invalid JSON\
✗ GPT-5 Thinking - Invalid JSON, suggested Python workaround\
✗ DeepHat-V1-7B - Refused task, provided Python script instead

**Why They Failed:**

The models understood the threats perfectly. They identified actors, malware, and TTPs correctly. But they couldn't format structured output to specification.

Claude generated sequential UUIDs (a1b2c3d4...) instead of random UUIDv4s. Copilot missed required properties on every object. Both used invalid STIX vocabularies.

The "thinking" models thought longer but performed worse - Copilot's enhanced reasoning produced 4x more errors than standard Claude.

**The Enterprise Risk:**

This isn't about AI being "bad" at security. It's about misaligned expectations.

Every vendor promises "AI-powered threat intelligence" but if models can't produce valid STIX, they can't integrate with your security stack. Your automation breaks. Your analysts debug AI output instead of hunting threats.

Teams trusting AI-generated threat intelligence could miss critical indicators. Downstream systems fail silently. Compliance requirements aren't met.

**What This Means for AI Governance:**

Before deploying AI in security operations, you need:

• Validation layers between AI and production systems
• Clear understanding of where AI helps vs. hinders
• Human review for structured data requirements
• Realistic expectations about AI capabilities

The gap between AI understanding and compliant output is wider than most realize. AI excels at recognizing threats but fails at the boring, critical work of proper formatting.

**The Path Forward:**

Use AI for what it's good at - understanding and analysis. Use deterministic code for what it's good at - structured output and compliance.

I'm building tools that bridge this gap, letting AI enhance security operations without breaking them. Next week: testing whether these same models can at least extract IOCs correctly.

What structured data challenges have you encountered with AI in production?

## Repository Contents
- `methodology.md` - Research design and approach
- `validation-results.md` - Detailed error analysis
- `seed-prompt.md` - Initial prompt given to all models
- `llm-generated-prompts/` - Each model's self-generated extraction prompt
- `outputs/` - Raw JSON outputs and validation logs

#AIGovernance #CyberSecurity #ThreatIntelligence #AIRisk #SecurityAutomation #ResponsibleAI #SecurityLeadership

---

*This research was conducted as part of the Cooper Cyber Coffee initiative - exploring practical AI applications in cybersecurity through hands-on experimentation.*
