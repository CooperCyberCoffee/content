# Validation Results: AI STIX Extraction Challenge

## Validation Pipeline

```
PDF Report → AI Model → Output → JSON Validation → STIX Validation → Production Ready?
                                        ↓                   ↓                ↓
                                    STAGE 1            STAGE 2           STAGE 3
```

## Stage 1: JSON Validation

### Test Method
```python
import json

def validate_json(output_string):
    try:
        json.loads(output_string)
        return True, "Valid JSON"
    except json.JSONDecodeError as e:
        return False, f"Invalid JSON: {str(e)}"
```

### Results

| Model | Valid JSON | Error Details |
|-------|------------|---------------|
| **Claude Opus 4.1** | ✅ Valid | Clean parse, properly escaped strings |
| **Copilot Think Deeper** | ⚠️ Valid* | First attempt incomplete (timeout), second attempt valid |
| **Gemini 2.5 Pro** | ❌ Failed | `Expecting ',' delimiter: line 47 column 5` - improper array formatting |
| **GPT-5 Thinking** | ❌ Failed | Mixed JSON with markdown formatting, included "```json" markers in output |
| **DeepHat-V1-7B** | N/A | No JSON output - provided Python script instead |

*Required two attempts 

## Stage 2: STIX 2.1 Schema Validation

### Test Method
```bash
# Install validator
pip install stix2-validator

# Run validation
stix2-validator --version 2.1 outputs/raw/[model]-output.json > outputs/validation/[model]-validation.log
```

### Claude Opus 4.1 - Validation Errors

**Total Errors/Warnings: 35+ violations**

Key failure patterns:
```
[!] Warning: bundle--a8f3c9e2-4b5d-4e6f-8a1b-9c2d3e4f5a6b
    {401} Custom property 'spec_version' should be implemented using an extension

[!] Warning: Multiple objects (33 instances)
    {103} Given ID value [object]--[sequential-hex] is not a valid UUIDv4 ID

[!] Warning: threat-actor--c1d2e3f4-5a6b-7c8d-9e0f-1a2b3c4d5e6f
    {211} primary_motivation contains value not in attack-motivation-ov vocabulary

[!] Warning: identity--a1b2c3d4-5e6f-7a8b-9c0d-1e2f3a4b5c6d
    {215} sectors contains value not in industry-sector-ov vocabulary

[!] Error: marking-definition--94868c89-83c2-464b-929b-a1a8aa3c8487
    TLP marking definitions must match those defined in STIX specification

[!] Error: indicator--d1e2f3a4-5b6c-7d8e-9f0a-1b2c3d4e5f6a
    Pattern failed to validate: FAIL: Error at line 1:27. mismatched input
```

**Analysis:** Claude made systematic errors:
- Used sequential hex patterns instead of UUIDv4 (e.g., `a1b2c3d4-5e6f-7a8b...`)
- Misunderstood STIX vocabularies and property requirements
- Incorrect TLP marking definition implementation
- Pattern syntax errors in indicators

### Copilot Think Deeper - Validation Errors  

**Total Errors/Warnings: 150+ violations**

Key failure patterns:
```
[!] Warning: bundle--9f1c3de4-2a4b-4a12-8f0d-6e5c3b2a1d4f
    {401} Custom property 'spec_version' should be implemented using an extension

[!] Error: All objects (20+ instances)
    Missing required properties: ['spec_version', 'created', 'modified']

[!] Error: All malware objects (6 instances)
    'is_family' is a required property

[!] Warning: Multiple objects (15 instances)
    {103} Given ID value [object]--[sequential-hex] is not a valid UUIDv4 ID

[!] Error: relationship objects
    Invalid references to malformed UUIDs in source_ref and target_ref
```

**Analysis:** Copilot had catastrophic structural failures:
- Missing fundamental required properties on nearly every object
- Used sequential patterns for UUIDs (similar to Claude but worse)
- Malware objects missing critical 'is_family' boolean
- Relationship objects pointing to invalid references

### Gemini 2.5 Pro & GPT-5 Thinking
Not tested - failed at JSON validation stage

### DeepHat-V1-7B
Not tested - no output to validate

## Stage 3: Production Readiness Assessment

### Critical Failures Preventing Production Use

| Failure Type | Impact | Models Affected |
|--------------|--------|-----------------|
| **Invalid JSON** | Complete pipeline failure | Gemini, GPT-5, DeepHat |
| **Invalid UUIDs** | Breaks object referencing | Claude, Copilot |
| **Missing Required Fields** | Rejected by STIX consumers | Claude, Copilot |
| **Wrong Pattern Syntax** | Indicators unusable | Claude |
| **Invalid Vocabulary** | Interoperability failure | Copilot |

## Key Insights

### Why Did Every Model Fail?

1. **UUID Generation Catastrophe:** Both Claude and Copilot generated sequential hex patterns (`a1b2c3d4-5e6f...`) instead of proper UUIDv4s - a fundamental misunderstanding of UUID requirements

2. **Missing Core Properties:** Copilot missed required fields on EVERY object (spec_version, created, modified), while Claude included them but formatted incorrectly

3. **Vocabulary Violations:** Models used invalid values for controlled vocabularies (e.g., wrong malware types, invalid relationship types, incorrect motivation values)

4. **Specification Confusion:** Models mixed STIX 2.0 and 2.1 syntax, showing they were likely trained on mixed or outdated examples

### The Error Magnitude Tells a Story:

| Model | Error Count | Primary Failure Mode |
|-------|------------|---------------------|
| Claude | 35+ errors | UUID format, vocabularies |
| Copilot | 150+ errors | Missing properties, UUIDs |
| Gemini | N/A | Failed at JSON stage |
| GPT-5 | N/A | Failed at JSON stage |
| DeepHat | N/A | Refused task entirely |

### The 0% Success Rate Reveals:

- **Sequential thinking breaks down:** Both models tried to create "neat" sequential UUIDs instead of random ones
- **More compute ≠ better structure:** Copilot's "Think Deeper" produced 4x more errors than standard Claude
- **LLMs understand threats, not schemas:** They extracted the right entities but couldn't format them correctly
- **This is a solvable problem:** All failures are formatting issues, not comprehension issues

## Recommendations

1. **Never trust AI-generated STIX without validation**
2. **Use AI for extraction, traditional code for formatting**
3. **Implement strict validation pipelines before production**
4. **Consider hybrid approaches: AI + deterministic formatting**

## Reproducibility

All validation outputs are available in `./outputs/validation/`:
- Full validator output logs
- Error summaries
- Raw model outputs for independent verification

To reproduce:
```bash
# Install validator
pip install stix2-validator

# Run validation
stix2-validator --version 2.1 outputs/raw/[model]-output.json > outputs/validation/[model]-validation.log
```

---

*This research was conducted as part of the Cooper Cyber Coffee initiative - exploring practical AI applications in cybersecurity through hands-on experimentation.*
