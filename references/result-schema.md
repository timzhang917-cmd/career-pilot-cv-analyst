# CareerPilot Result Schema

Use this schema to construct the structured analysis result before generating the HTML report.

The result must be one valid JSON object. Use exactly the top-level fields, nested fields, and enum values defined below.

Do not include Markdown, comments, trailing commas, `NaN`, or explanatory text outside the JSON object.

## Top-level structure

The result object must contain exactly these fields:

```json
{
  "target_job_title": "",
  "report_language": "",
  "overall": {},
  "category_scores": {},
  "strengths": [],
  "gaps": [],
  "requirements": [],
  "must_fix_issues": [],
  "suggestions": []
}
```

Do not remove, rename, or add top-level fields.

All descriptive strings must follow the language rules in `analysis-rubric.md`.

All strings must contain plain text only. Do not place HTML markup, Markdown formatting, scripts, or style declarations inside result values.

## `target_job_title`

Type: string

Use the job title supplied by the user.

If the user does not provide a title:

1. Use an explicit job title found in the job description.
2. If no reliable title can be identified, use a translated equivalent of `Target Role` in the report language.

Do not invent a specialised title that is not supported by the job description.

Example:

```json
"target_job_title": "Backend Developer"
```

## `report_language`

Type: string

Record the natural-language name of the language used for descriptive report content.

Examples:

```json
"report_language": "English"
```

```json
"report_language": "Simplified Chinese"
```

Do not use this field to change internal JSON field names or enum values.

## `overall`

Type: object

Required fields:

```json
{
  "score": 0,
  "level": "Low Match",
  "summary": ""
}
```

### `overall.score`

Type: integer  
Allowed range: 0–100

Calculate this value using the applicable category scores and normalised weights defined in `analysis-rubric.md`.

Do not include a percent symbol.

### `overall.level`

Type: string

Allowed values:

- `Excellent Match`
- `Good Match`
- `Moderate Match`
- `Weak Match`
- `Low Match`

The value must correspond to `overall.score`:

| Score | Level |
|---|---|
| 85–100 | `Excellent Match` |
| 70–84 | `Good Match` |
| 55–69 | `Moderate Match` |
| 40–54 | `Weak Match` |
| 0–39 | `Low Match` |

### `overall.summary`

Type: string

Provide a concise evidence-based summary of the overall match.

The summary should:

- Identify the strongest areas of alignment.
- Identify the most material gaps or unsupported requirements.
- Mention an important non-scored constraint when relevant.
- Avoid repeating every category score.
- Avoid making a hiring recommendation.
- Avoid claiming that missing evidence proves inability.

## `category_scores`

Type: object

Required fields:

```json
{
  "hard_skills": null,
  "soft_skills": null,
  "experience": null,
  "education": null
}
```

Each value must be either:

- An integer from 0 to 100 when the job description contains at least one requirement in that category.
- JSON `null` when the job description contains no substantive requirement in that category.

Do not use strings such as `"N/A"`, `"null"`, or `"-"` in the structured data. The HTML renderer converts JSON `null` to the visible label `N/A`.

Category scores must be reproducible from the entries in `requirements`.

Requirements classified as `other` must not affect category scores.

## `strengths`

Type: array of objects

Each object must contain exactly:

```json
{
  "title": "",
  "evidence": ""
}
```

### `title`

Type: string

State one concise job-relevant strength.

### `evidence`

Type: string

Explain the submitted evidence supporting the strength.

If the supporting information comes only from supplementary material, state that explicitly.

Do not add a strength without identifiable evidence. If no meaningful strength is supported, return an empty array.

Example:

```json
{
  "title": "Relevant Python and Django experience",
  "evidence": "The CV describes the development of a Django web application using Python and a relational database."
}
```

## `gaps`

Type: array of objects

Each object must contain exactly:

```json
{
  "gap": "",
  "impact": ""
}
```

### `gap`

Type: string

Describe a material mismatch, partial match, or missing-evidence area.

Write it as a limitation of the submitted evidence, not as an absolute statement about the candidate.

### `impact`

Type: string

Explain why the gap matters for the target job.

Do not repeat the same gap in several objects. If no material gap exists, return an empty array.

Example:

```json
{
  "gap": "No supporting evidence of AWS experience was found.",
  "impact": "AWS is listed as a preferred technology in the target job description."
}
```

## `requirements`

Type: array of objects

The array must contain one object for every substantive candidate-related requirement identified from the job description.

Preserve the original requirement order.

Each object must contain exactly:

```json
{
  "requirement": "",
  "category": "hard_skills",
  "importance": "Standard",
  "evidence": "",
  "evidence_source": "CV",
  "assessment": "Basic Match"
}
```

### `requirement`

Type: string

Restate the requirement concisely without materially changing its meaning.

Do not add a requirement that is not present in the job description.

### `category`

Type: string

Allowed values:

- `hard_skills`
- `soft_skills`
- `experience`
- `education`
- `other`

Use `other` for relevant constraints that do not represent one of the four scored categories.

### `importance`

Type: string

Allowed values:

- `Required`
- `Standard`
- `Preferred`

These values correspond to the rubric weights:

| Importance | Weight |
|---|---:|
| `Required` | 3 |
| `Standard` | 2 |
| `Preferred` | 1 |

Assign the value from the meaning and wording of the job description.

### `evidence`

Type: string

Describe the relevant evidence found in the submitted materials.

If no evidence is found, use the missing-evidence phrase required by `analysis-rubric.md`.

Do not use the job description itself as candidate evidence.

### `evidence_source`

Type: string

Allowed values:

- `CV`
- `Supplementary Material`
- `CV and Supplementary Material`
- `None`

Apply the following rules:

- Use `CV` when the supporting evidence appears in the CV.
- Use `Supplementary Material` when it appears only in supplementary material.
- Use `CV and Supplementary Material` when both sources materially support the assessment.
- Use `None` when no supporting evidence is found.

### `assessment`

Type: string

Allowed values:

- `Strong Match`
- `Basic Match`
- `Weak Match`
- `Gap`
- `Clear Gap`

Apply the definitions and points in `analysis-rubric.md`.

Cross-field rules:

- `evidence_source: "None"` requires `assessment: "Clear Gap"`.
- `assessment: "Clear Gap"` requires `evidence_source: "None"`.
- `evidence_source: "Supplementary Material"` must not receive `Strong Match`.
- `Gap` requires evidence that directly demonstrates a shortfall.
- A requirement in category `other` is displayed but excluded from score calculations.

## `must_fix_issues`

Type: array of objects

Each object must contain exactly:

```json
{
  "location": "",
  "problem": "",
  "why_it_matters": "",
  "suggested_correction": ""
}
```

### `location`

Type: string

Identify the CV section, entry, sentence, or item where the definite problem appears.

### `problem`

Type: string

Describe the exact error or contradiction.

### `why_it_matters`

Type: string

Explain the likely effect on clarity, credibility, or interpretation.

### `suggested_correction`

Type: string

Provide a safe correction that does not introduce unsupported facts.

Use the strict must-fix criteria in `analysis-rubric.md`.

Do not include ordinary weaknesses, missing evidence, optional enhancements, or general wording improvements.

If no definite must-fix issue is found, return:

```json
"must_fix_issues": []
```

## `suggestions`

Type: array of objects

Each object must contain exactly:

```json
{
  "priority": "Medium",
  "target_section": "",
  "recommendation": "",
  "reason": ""
}
```

### `priority`

Type: string

Allowed values:

- `High`
- `Medium`
- `Low`

Use the priority definitions in `analysis-rubric.md`.

### `target_section`

Type: string

Identify the CV section or subject to which the recommendation applies.

Examples include:

- `Professional Summary`
- `Work Experience`
- `Project Experience`
- `Technical Skills`
- `Education`
- `Application Information`

Use a translated equivalent when the report language is not English.

### `recommendation`

Type: string

Provide one specific, practical action.

Do not recommend adding skills, experience, achievements, or metrics that the candidate cannot verify.

### `reason`

Type: string

Link the recommendation to a job requirement, evidence gap, or clarity problem.

If no useful and evidence-based suggestion can be made, return an empty array.

## Validation rules

Before rendering the report, validate all of the following:

1. The result is valid JSON.
2. All required top-level fields are present.
3. No unknown top-level fields are present.
4. All nested objects contain the required fields.
5. Fixed enum values match exactly, including capitalisation.
6. Scores are integers from 0 to 100 or permitted JSON `null` values.
7. The requirement array follows the original job-description order.
8. Every scored requirement has a valid category and importance.
9. Category scores match the weighted requirements in that category.
10. A category is `null` only when no requirement belongs to that category.
11. The overall score matches the normalised category calculation.
12. The overall level matches the overall score.
13. Requirements in category `other` are excluded from numerical scoring.
14. Evidence-source and assessment combinations follow the cross-field rules.
15. Strings contain plain text rather than HTML or executable content.
16. No unsupported personal detail, qualification, achievement, or metric has been introduced.

If validation fails, correct the result object before running the HTML renderer.

## Complete example

The following example demonstrates the required structure. Its content is illustrative and must not be copied into a real analysis.

```json
{
  "target_job_title": "Backend Developer",
  "report_language": "English",
  "overall": {
    "score": 62,
    "level": "Moderate Match",
    "summary": "The candidate provides strong evidence of Python and Django development and meets the stated education requirement. However, the submitted materials show less experience than the required minimum, provide no supporting evidence of AWS experience, and do not confirm the stated work-authorisation requirement."
  },
  "category_scores": {
    "hard_skills": 73,
    "soft_skills": 70,
    "experience": 20,
    "education": 100
  },
  "strengths": [
    {
      "title": "Relevant Python and Django experience",
      "evidence": "The CV describes the development of a Django web application using Python and a relational database."
    },
    {
      "title": "Education requirement met",
      "evidence": "The CV lists a completed bachelor's degree in Computer Science, matching the stated education requirement."
    }
  ],
  "gaps": [
    {
      "gap": "The CV demonstrates two years of relevant backend development experience rather than the required three years.",
      "impact": "The role identifies three years of backend development experience as a minimum requirement."
    },
    {
      "gap": "No supporting evidence of AWS experience was found.",
      "impact": "AWS is listed as a preferred technology for the role."
    },
    {
      "gap": "No supporting evidence of the required work authorisation was found.",
      "impact": "The job description identifies existing work authorisation as a required application condition."
    }
  ],
  "requirements": [
    {
      "requirement": "Professional experience with Python and Django",
      "category": "hard_skills",
      "importance": "Required",
      "evidence": "The CV describes building a web application with Python and Django.",
      "evidence_source": "CV",
      "assessment": "Strong Match"
    },
    {
      "requirement": "Experience designing REST APIs",
      "category": "hard_skills",
      "importance": "Standard",
      "evidence": "The CV mentions developing API endpoints, but does not describe the API design scope or standards used.",
      "evidence_source": "CV",
      "assessment": "Basic Match"
    },
    {
      "requirement": "AWS experience",
      "category": "hard_skills",
      "importance": "Preferred",
      "evidence": "No supporting evidence found.",
      "evidence_source": "None",
      "assessment": "Clear Gap"
    },
    {
      "requirement": "At least three years of backend development experience",
      "category": "experience",
      "importance": "Required",
      "evidence": "The employment dates in the CV demonstrate two years of relevant backend development experience.",
      "evidence_source": "CV",
      "assessment": "Gap"
    },
    {
      "requirement": "Ability to collaborate effectively within a development team",
      "category": "soft_skills",
      "importance": "Standard",
      "evidence": "The CV describes participation in a team software project but provides limited detail about collaboration responsibilities.",
      "evidence_source": "CV",
      "assessment": "Basic Match"
    },
    {
      "requirement": "Bachelor's degree in Computer Science or a related field",
      "category": "education",
      "importance": "Standard",
      "evidence": "The CV lists a completed bachelor's degree in Computer Science.",
      "evidence_source": "CV",
      "assessment": "Strong Match"
    },
    {
      "requirement": "Existing authorisation to work in the stated location",
      "category": "other",
      "importance": "Required",
      "evidence": "No supporting evidence found.",
      "evidence_source": "None",
      "assessment": "Clear Gap"
    }
  ],
  "must_fix_issues": [],
  "suggestions": [
    {
      "priority": "High",
      "target_section": "Work Experience",
      "recommendation": "Clarify the duration and scope of relevant backend work across each position so that the total experience can be verified easily.",
      "reason": "The target role requires three years of backend development experience, while the current CV clearly demonstrates two years."
    },
    {
      "priority": "Medium",
      "target_section": "Technical Skills",
      "recommendation": "If AWS has been used in a verified project or role, identify the specific services, context, and contribution; otherwise do not add it as a claimed skill.",
      "reason": "AWS is preferred by the employer, but the submitted materials contain no supporting evidence."
    },
    {
      "priority": "Medium",
      "target_section": "Application Information",
      "recommendation": "Clarify work-authorisation status where appropriate and accurate.",
      "reason": "The job description treats existing work authorisation as a required application condition."
    }
  ]
}
```
