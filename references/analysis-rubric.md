# CareerPilot Analysis Rubric

Use this rubric to compare the candidate's submitted materials with the target job description. Apply it before constructing the result object defined in `result-schema.md`.

## Purpose and interpretation

CareerPilot measures how strongly the evidence provided by the candidate matches the target job description.

The resulting scores:

- Represent evidence-based job matching, not the candidate's overall ability or potential.
- Are not hiring decisions and must not be presented as guarantees of interview or employment outcomes.
- Must be based only on the CV, job description, and supplementary materials supplied by the user.
- Must not be influenced by irrelevant or protected personal characteristics.

Do not use a candidate's name, age, gender, photograph, ethnicity, marital status, disability, religion, or similar personal characteristics when assessing job fit. Consider work authorisation or location only when the job description explicitly makes it a relevant condition and the submitted materials contain supporting evidence.

## Language rules

Determine the dominant language of the job description before beginning the analysis.

Use that language consistently for all descriptive report content, including:

- Summaries
- Strengths
- Gaps
- Requirements
- Evidence
- Problems
- Corrections
- Recommendations
- Reasons

Quoted source text, proper nouns, product names, and necessary technical abbreviations may remain in their original language.

Keep all internal field names and fixed enum values defined in `result-schema.md` unchanged.

When no supporting evidence is found, use:

- `No supporting evidence found.` for an English report.
- `未找到支持性证据。` for a Simplified Chinese report.
- A direct equivalent in the report language for other languages.

Do not mix languages unnecessarily.

## Evidence rules

Use the following evidence hierarchy.

### 1. Direct CV evidence

Direct CV evidence includes clearly stated:

- Skills and technologies
- Employment or project responsibilities
- Achievements and outcomes
- Dates and durations
- Education and qualifications
- Certificates
- Portfolio or professional activities

Direct evidence should identify what the candidate did, used, achieved, or completed.

A keyword alone is weaker than evidence showing how or where the skill was applied.

### 2. Supplementary evidence

Supplementary materials may support the analysis, but clearly identify that the evidence comes from supplementary material rather than the CV.

Evidence found only in supplementary materials must not receive a `Strong Match` assessment. It may receive a `Basic Match` when it is direct, specific, and credible. Otherwise, assess it as a `Weak Match`.

When relevant verified information appears only in supplementary materials, recommend adding it to the CV where appropriate.

### 3. Indirect evidence

Indirect evidence may indicate related experience but does not directly prove the requirement.

For example, general web development experience does not automatically prove experience with a specific framework, cloud platform, testing method, or deployment environment.

Do not treat adjacent knowledge as equivalent to the required skill without clear support.

### 4. Missing evidence

If the supplied materials do not support a requirement:

- Use the required missing-evidence phrase.
- Assess the requirement as `Clear Gap`.
- Do not claim that the candidate definitely lacks the skill or experience.
- Explain only that the submitted materials do not demonstrate it.

The job description defines what the employer requests. It must never be treated as evidence of what the candidate can do.

## Job description coverage

Analyse the entire job description, regardless of its headings or formatting.

Include all substantive candidate-related requirements, including:

- Duties and responsibilities
- Technical and professional skills
- Tools, platforms, and methodologies
- Experience requirements
- Soft skills
- Education and qualification requirements
- Relevant employment conditions or constraints

Exclude content that does not describe candidate requirements, such as:

- General company promotion
- Employee benefits
- Equal-opportunity statements
- Application instructions
- Legal or privacy notices

For numbered or bulleted requirements, normally create one requirement entry for each original item.

For unstructured paragraphs, identify each distinct candidate-related requirement.

Preserve the original order. Do not omit, merge, or unnecessarily split requirements.

Classify each requirement as one of the following:

- `hard_skills`
- `soft_skills`
- `experience`
- `education`
- `other`

The `other` category includes administrative or contextual conditions such as work location, work authorisation, travel, schedule, or employment arrangement. Display these requirements in the comparison, but do not include them in the four category scores.

## Requirement assessments

Use the following fixed assessment values.

| Assessment | Points | Definition |
|---|---:|---|
| `Strong Match` | 100 | Direct, specific evidence fully meets or exceeds the requirement. |
| `Basic Match` | 70 | Direct evidence supports the main requirement, but its depth, recency, scope, or context is not fully demonstrated. |
| `Weak Match` | 40 | Evidence is indirect, limited, weakly related, or found only in unverified supplementary information. |
| `Gap` | 20 | The materials provide relevant evidence but also clearly show that an explicit threshold or part of the requirement is not met. |
| `Clear Gap` | 0 | No supporting evidence is found for the requirement. |

Apply these distinctions consistently:

- Use `Weak Match` when some related evidence exists but does not directly establish the requirement.
- Use `Gap` when the evidence itself demonstrates a shortfall, such as two years of experience where five years are explicitly required.
- Use `Clear Gap` when there is no relevant evidence.
- Do not increase an assessment because a requirement is common, likely, or easy to learn.
- Do not reduce an assessment because of an unsupported assumption.

## Requirement importance

Assign an importance weight to each scored requirement:

| Job-description wording | Weight |
|---|---:|
| Required, essential, must, mandatory, minimum, or equivalent wording | 3 |
| A normal responsibility or requirement without an explicit priority label | 2 |
| Preferred, desirable, beneficial, nice to have, or equivalent wording | 1 |

Use the meaning of the job description rather than matching only these exact English words.

Do not assign an artificial priority when the job description does not provide enough context.

## Category scores

Calculate scores for:

- `hard_skills`
- `soft_skills`
- `experience`
- `education`

For each applicable category:

1. Take the point value of every requirement assigned to that category.
2. Multiply each point value by its requirement importance weight.
3. Add the weighted values.
4. Divide by the sum of the importance weights.
5. Round the result to the nearest integer.

Use this formula:

`category score = round(sum(assessment points × importance weight) ÷ sum(importance weights))`

If the job description contains no substantive requirement for a category:

- Set that category score to `null`.
- Display it as `N/A` in the report.
- Exclude it from the overall-score calculation.

Do not create requirements merely to ensure that every category receives a numerical score.

## Education assessment

Assess education only against education requirements explicitly stated or clearly implied by the target job description.

Relevant factors may include:

- Required qualification level
- Required or preferred field of study
- Professional accreditation
- Directly relevant academic training

Apply the normal requirement-assessment definitions:

- Use `Strong Match` when the submitted education clearly meets or exceeds the stated requirement.
- Use `Basic Match` when the qualification level is sufficient but the field or relevance is only partially aligned.
- Use `Weak Match` when the education is indirectly related or insufficiently described.
- Use `Gap` when the supplied education clearly falls below an explicit requirement.
- Use `Clear Gap` when education is explicitly required but no supporting evidence is found.

Do not score education according to:

- University rankings
- Institutional prestige
- Public or private status
- Assumed reputation
- Country-specific university tiers

Do not guess the status or quality of an institution from its name.

If the job description does not include a meaningful education requirement, set the education score to `null`.

## Overall score

Use these base category weights:

| Category | Base weight |
|---|---:|
| Hard skills | 0.30 |
| Experience | 0.30 |
| Soft skills | 0.20 |
| Education | 0.20 |

Exclude categories whose score is `null`, then normalise the remaining weights.

Use this formula:

`overall score = round(sum(applicable category score × base weight) ÷ sum(applicable base weights))`

For example, if education is not applicable:

`overall score = round((hard skills × 0.30 + experience × 0.30 + soft skills × 0.20) ÷ 0.80)`

Set the overall level according to the final score:

| Score | Level |
|---|---|
| 85–100 | `Excellent Match` |
| 70–84 | `Good Match` |
| 55–69 | `Moderate Match` |
| 40–54 | `Weak Match` |
| 0–39 | `Low Match` |

All scores must be integers from 0 to 100.

Do not include a percent symbol in stored score values. The HTML renderer is responsible for displaying score symbols.

Requirements classified as `other` do not affect the numerical score, but important constraints should be clearly reported in the summary or gaps.

## Strengths

Include strengths that are relevant to the target job and supported by identifiable evidence.

A strength should:

- Refer to a meaningful job requirement.
- Explain the relevant evidence.
- Avoid repeating the same point using different wording.
- Avoid generic praise.
- Avoid claims that cannot be traced to the submitted materials.

Do not add strengths merely to balance the number of gaps.

## Gaps

Include material mismatches, partial matches, and missing evidence that may affect the application.

Write gaps as evidence limitations rather than absolute claims about the candidate.

Prefer:

> No supporting evidence of automated testing was found in the submitted CV.

Avoid:

> The candidate does not know automated testing.

Do not list the same issue repeatedly across several gap statements.

## Must-fix issues

Reserve `must_fix_issues` for definite and consequential problems that should be corrected before the CV is submitted.

Include an issue only when it is directly supported by the CV text and meets at least one of these conditions:

- A clear spelling, grammar, or sentence-completion error damages meaning or professional credibility.
- Dates, roles, qualifications, skills, employment details, or numerical claims contradict one another.
- A timeline, date range, achievement, or statement is impossible or logically inconsistent.
- Wording could materially misrepresent the candidate's experience or qualifications.
- A company name, job title, contact detail, or other critical item is clearly incorrect.
- Another definite error is likely to cause serious confusion or loss of credibility.

Do not classify the following as must-fix issues:

- Missing evidence for a job requirement
- A skill or experience gap
- A vague but understandable statement
- Missing metrics or insufficient detail
- A general opportunity to improve impact
- An optional enhancement
- A visual formatting concern
- A possible text-extraction error
- An issue based on speculation

Place these non-critical matters under gaps or suggestions.

For each must-fix issue:

- Identify the specific location.
- State the exact problem.
- Explain why it matters.
- Provide a correction that does not invent new facts.

Do not create an issue merely to populate the section. If no definite must-fix issue is found, return an empty array.

## Suggestions

Provide practical, targeted recommendations connected to identified evidence or gaps.

Every suggestion must include:

- A priority
- The CV section or subject it targets
- A specific recommended action
- A reason linked to the job description or analysis

Use these priorities:

- `High`: Addresses a central required qualification, a major evidence gap, or an issue likely to materially affect the application.
- `Medium`: Strengthens relevant evidence, clarity, specificity, or alignment.
- `Low`: Provides a useful but non-essential improvement.

Suggestions may recommend:

- Adding verified experience currently found only in supplementary materials
- Clarifying where and how a skill was used
- Reordering relevant content
- Adding a verified outcome or metric
- Making the connection between existing experience and a job requirement clearer

Suggestions must not:

- Tell the candidate to claim a skill or experience they do not have
- Invent achievements, responsibilities, employers, dates, or metrics
- Encourage misleading wording
- Offer generic advice without identifying where it applies

When recommending a metric, ask the candidate to add it only if it can be verified.

## Final consistency checks

Before producing the result object, verify that:

- Every substantive job requirement has been assessed.
- Requirement order follows the job description.
- Every assessment is supported by its evidence text.
- Missing evidence is not presented as proof of inability.
- Supplementary-only evidence is clearly labelled.
- Category calculations use the correct points and importance weights.
- Categories without relevant requirements are `null`.
- The overall score excludes non-applicable categories and uses normalised weights.
- The overall level matches the overall score.
- Strengths, gaps, must-fix issues, and suggestions do not contradict one another.
- Must-fix issues satisfy the strict must-fix criteria.
- No unsupported qualification, achievement, or personal detail has been introduced.
- Protected or irrelevant personal characteristics have not affected the analysis.
- Descriptive content uses the selected report language consistently.
