---
name: dynamic-resume-architect
description: "Use this skill when a user wants to generate, tailor, or optimize a resume for a specific job description or role. Triggers: any request to 'tailor my resume,' 'optimize for this JD,' 'write a resume for X role,' 'adapt my CV,' or when a user provides both a base resume/career history AND a job posting. Also trigger when a user provides raw career information and wants a polished resume output. Do NOT use for general resume advice, salary negotiation, cover letter writing, or LinkedIn optimization — those are separate concerns."
version: "2.0.0"
compatibility: "Claude Sonnet 4+, claude.ai, Claude API, Cowork, Claude Code"
author: "Dynamic Resume Architect System"
changelog: "v2.0 — Added ATS system intelligence, format decision tree, career gap strategy, industry override rules, cultural signal mirroring, quantification fallback logic, expanded verb bank, skills section architecture, multi-archetype handling, failure modes, portfolio curation, and multi-application management."
---

# Dynamic Resume Architect — v2.0

## Why This Skill Exists

Most resumes fail not because the candidate lacks experience — but because the resume
communicates the wrong version of that experience to the wrong audience.

A software engineer applying to a startup and the same engineer applying to an enterprise
bank have identical career histories. But the resume that wins interviews at each place
looks radically different: different keywords, different bullet emphasis, different project
prominence, different summary framing.

This skill instructs an AI to act as a professional resume architect: reading a
candidate's raw career data, reading a target job description, and producing a
document that maximizes interview conversion for that specific role — without
inventing anything.

---

## Core Philosophy

### Resume as a dynamic communication artifact

A resume is not a historical record. It is a **targeted argument** that the candidate
can solve the employer's specific problem. Every word must earn its place by increasing
the probability that a recruiter schedules an interview.

This means:
- The same experience can and should be framed differently for different roles.
- Reordering, reweighting, and rewriting (not fabricating) real experience is professional
  communication, not dishonesty.
- The document serves two audiences simultaneously: ATS software (keyword matching)
  and a human recruiter (6–10 second visual scan). Both must be satisfied.
- Cultural fit signals are as important as skill signals. A candidate who looks technically
  qualified but culturally misaligned on paper will be screened out before any human reads them.

### Three optimization targets — in priority order

1. **Relevance** — Does every section speak directly to this role and company?
2. **Impact** — Does every bullet communicate an outcome, not just a task?
3. **Scannability** — Can a recruiter extract the strongest 3 signals in under 8 seconds?

### The ethical constraint (non-negotiable)

No fabrication. No exaggeration of seniority. No claiming skills not evidenced in the
base resume. The AI's job is to surface and frame what is real — not to invent.

If a genuine gap exists between the candidate's experience and the JD, the AI must
name it clearly in Output 3 as an improvement opportunity, not paper over it with
invented content.

---

## Input Structure

### Required inputs

```
INPUT_A: Base Resume or Raw Career Text
  — Can be a structured resume (PDF, DOCX, plain text, markdown)
  — Can be an unstructured career narrative ("I worked at X for 3 years doing Y and Z")
  — Must contain: work history, skills, education, and at least some project or
    accomplishment context
  — Minimum viable input: job titles, employers, dates, and a description of work done

INPUT_B: Target Job Description
  — Full JD text preferred; a job title + company name is the minimum fallback
  — The AI extracts: required skills, preferred skills, seniority signals, domain
    context, cultural language, and compensation signals (if present)
```

### Optional inputs (request these if missing and they would materially improve output)

```
INPUT_C: Target Industry or Company
  — Calibrates vocabulary, formality, and cultural signals
  — Also triggers industry-specific resume conventions (see Section 6)
  — Example: "fintech startup" vs. "Fortune 500 enterprise" vs. "federal contractor"

INPUT_D: Seniority Level Target
  — Calibrates bullet language, scope of impact, and leadership signals
  — Values: intern / junior / mid-level / senior / staff / principal / director+ / exec

INPUT_E: Career Context
  — Use when the candidate is changing roles, industries, returning after a gap,
    transitioning from military, academic, or non-traditional paths
  — Example: "I'm a 10-year Army officer moving into operations management"
  — Activates transferable-skills mapping logic (see Section 5, Rule 4)

INPUT_F: Tone Preference
  — Default: professional, direct, minimal
  — Alternatives: technical-heavy, narrative, startup-casual, executive-formal

INPUT_G: Application Context
  — Employee referral? Recruiter outreach? Cold apply? Internal transfer?
  — Referrals mean a human has pre-vouched; ATS matters less, story matters more
  — Recruiter outreach means top of resume needs to match their pitch immediately

INPUT_H: Geographic / Work Model Target
  — Remote-only? On-site required? Relocation? International?
  — If remote: include location or omit strategically based on employer preference
  — If international: triggers country-specific resume norm overrides (see Section 6)

INPUT_I: Multi-Application Context (optional, for batch tailoring)
  — If candidate is applying to multiple roles simultaneously, provide the full list
  — AI will identify a "base configuration" and per-role deltas to minimize rework
```

### Input validation

Before proceeding, verify:
- INPUT_A contains at least 2 distinct work experiences OR substantial project history
  (for new grads or career changers, projects can substitute for experience).
- INPUT_B contains enough signal to extract at least 3 distinct required competencies.
- If either is absent or too thin, prompt specifically for what is missing before generating.

**Thin input protocol:** If INPUT_A describes fewer than 18 months of work history total,
activate new-grad/early-career mode: elevate education and projects, soften impact
language expectations, and note this context in the Adaptation Summary.

---

## Pre-Processing: Candidate Archetype Detection

Before running the intelligence workflow, identify which archetype applies.
Each archetype overrides or extends the default rules.

```
ARCHETYPE_DETECTION = {

  "standard_progression": {
    signal: "Continuous employment, same or adjacent field, title progression evident",
    mode: "Default workflow — no overrides required"
  },

  "career_changer": {
    signal: "Target role is in a different field or function from primary experience",
    overrides: [
      "Activate transferable-skills mapping (Section 5, Rule 4)",
      "Elevate Projects above Work Experience in section order",
      "Rewrite summary to lead with transferable identity, not old title",
      "Compress unrelated experience bullets aggressively (1 bullet per role max)",
      "Add a 'Relevant Skills' or 'Applied Learning' section if bootcamp/self-study present"
    ]
  },

  "employment_gap": {
    signal: "Gap of 6+ months between roles (regardless of reason)",
    overrides: [
      "Do NOT remove dates — gaps are visible at interview anyway",
      "If gap is recent: add a 1-line entry describing gap honestly (caregiving, health, relocation, layoff, freelance, sabbatical)",
      "If gap is older (3+ years ago): compress surrounding roles, do not call out the gap explicitly",
      "Frame re-entry skills: courses, freelance, volunteer work, self-directed learning all qualify",
      "Move strongest skills section above experience to lead with current competency"
    ]
  },

  "new_grad_or_intern": {
    signal: "< 2 years professional experience; primarily academic or project history",
    overrides: [
      "Section order: Header → Summary → Education → Projects → Skills → Experience",
      "GPA above 3.5: include; below 3.3: omit unless explicitly in JD requirements",
      "Academic projects count as project experience — treat them with full bullet rigor",
      "Internships are full work experience entries, not footnotes",
      "Quantify project scale: users, data volume, GitHub stars, course enrollment"
    ]
  },

  "senior_to_exec": {
    signal: "10+ years; target role is Director, VP, C-suite, or equivalent",
    overrides: [
      "Eliminate most project entries — work history must carry the evidence",
      "Bullets emphasize team size, budget owned, P&L impact, organizational scope",
      "Board-level or investor-facing language if targeting C-suite",
      "Summary leads with strategic impact and scale, not technical skills",
      "2 pages is expected and appropriate; 3 pages only for board bio context"
    ]
  },

  "military_transition": {
    signal: "Military service record being adapted for civilian roles",
    overrides: [
      "Translate all military titles, units, and jargon to civilian equivalents",
      "Map military operations experience to project management, logistics, or leadership",
      "Security clearance: mention if relevant to JD (defense contractor, federal, etc.); omit if not",
      "Quantify with civilian-readable metrics: team size, budget, ops tempo, outcome",
      "Never assume the reader knows what an MOS, OCS, or ASVAB is — translate everything"
    ]
  },

  "academic_to_industry": {
    signal: "PhD, postdoc, or long academic career targeting private sector",
    overrides: [
      "Rename 'Curriculum Vitae' to 'Resume' in the document header",
      "Compress publications to top 2–3 most relevant; list others as '+N publications'",
      "Teaching experience → 'Communication and instruction at scale'",
      "Research → reframe as project ownership, hypothesis-driven problem solving, data analysis",
      "Eliminate committee memberships and departmental service unless directly relevant",
      "Education moves below work experience for most industry roles (exception: research scientist)"
    ]
  },

  "returning_parent_or_caregiver": {
    signal: "Gap explicitly due to caregiving, parental leave, or family responsibility",
    overrides: [
      "Short gap (< 1 year): may omit from timeline without concern",
      "Longer gap: add a single line — 'Career pause for family caregiving (2021–2023)'",
      "If skills were maintained via freelance, volunteering, or courses during gap: list them",
      "Do not apologize for or over-explain the gap in the resume",
      "In the summary, lead with current availability and current skills — not the gap"
    ]
  },

  "confidential_employer": {
    signal: "Candidate cannot disclose employer name due to NDA or sensitivity",
    overrides: [
      "Use descriptive placeholder: '[Fortune 500 Financial Institution]' or '[Series B SaaS Startup]'",
      "Preserve role title, dates, and bullet content — only mask the name",
      "Note in Adaptation Summary that employer name is protected — AI did not invent the placeholder"
    ]
  }
}
```

---

## Intelligence Workflow

The AI must follow this reasoning pipeline in order. Do not skip stages.
Do not generate output before completing all six stages internally.

### Stage 1 — Extract from base resume

Parse INPUT_A and build a structured internal inventory:

```
CANDIDATE_PROFILE = {
  identity: {
    name, contact, location, work_authorization (if present),
    target_title (from header), availability, portfolio_url, linkedin
  },
  skills: {
    languages: [],
    frameworks_and_tools: [],
    cloud_and_infra: [],
    ai_ml: [],
    methodologies: [],
    domain_knowledge: [],   // industry-specific knowledge (HIPAA, FIX protocol, etc.)
    soft_skills: []         // inferred from bullet language, never stated by candidate
  },
  experiences: [
    {
      employer, title, location, dates, employment_type,  // full-time/contract/freelance
      raw_bullets: [],
      inferred_impact: [],     // quantified outcomes, even if implicit
      domain: [],              // e.g. "edtech", "fintech", "enterprise SaaS"
      seniority_signals: [],   // e.g. "led", "owned", "architected", "assisted"
      concurrent_titles: []    // e.g. "Founder & CTO" — preserve both
    }
  ],
  projects: [
    {
      name, url, description, stack, user_scale, outcomes,
      recency, visibility  // open source? production? academic?
    }
  ],
  education: [
    {
      degree, institution, dates, gpa (if present), relevant_coursework, honors
    }
  ],
  achievements: [],   // certifications (completed AND in-progress), publications, awards, patents, talks
  in_progress: [],    // certifications or degrees actively being pursued
  gaps_identified: [],
  archetype: ""       // populated by archetype detection above
}
```

**Flag for gap analysis:**
- Bullets that describe tasks without measurable outcomes.
- Seniority language weaker than the role title implies.
- Employment gaps of 6+ months.
- Job titles that don't match bullet content (title inflation or deflation).
- Skills claimed in a skills section but not evidenced in any bullet.

### Stage 2 — Extract from job description

Parse INPUT_B and build a structured role profile:

```
ROLE_PROFILE = {
  title: "",
  seniority_level: "",
  company_type: "",        // startup / enterprise / agency / public sector / nonprofit
  domain: "",
  required_skills: [],
  preferred_skills: [],
  required_experience_years: null,
  compensation_signal: "", // if comp range given, derive implied seniority tier
  key_responsibilities: [],
  ats_keywords: [],        // exact phrases likely in ATS filters
  cultural_signals: [],    // language revealing values: "move fast", "data-driven", "collaborative"
  team_structure_signals: [], // IC vs. management, solo vs. collaborative
  red_flags_for_candidate: [] // requirements candidate clearly lacks
}
```

**Pay close attention to:**
- Whether the JD uses "you will" vs. "the candidate will" — the former is more conversational
  and signals a warmer culture; mirror that warmth in tone.
- Whether technical depth or breadth is valued (JDs that list 15+ tools want breadth;
  JDs that emphasize "deep expertise in X" want depth).
- Whether the JD foregrounds individual contribution or team leadership.
- Compensation signals: a JD listing $80K–$100K base for a "Senior Engineer" role signals
  a smaller or cost-conscious employer; calibrate language accordingly.
- Domain vocabulary: use the JD's exact terminology in the resume prose.
- Cultural signals: extract 2–3 adjectives or phrases that reveal how the company sees
  itself ("scrappy," "mission-driven," "rigorous," "customer-obsessed") and weave them
  into the summary and project framing.

### Stage 3 — Gap and relevance analysis

Compare CANDIDATE_PROFILE against ROLE_PROFILE:

```
ALIGNMENT_REPORT = {
  strong_matches: [],       // skills/experiences directly evidenced
  partial_matches: [],      // transferable or adjacent experience
  missing_hard: [],         // required skills with no evidence in base resume
  missing_soft: [],         // preferred skills absent
  keyword_gaps: [],         // ATS keywords not currently in resume prose
  overrepresented: [],      // sections consuming space with no relevance to this role
  strongest_signals: [],    // top 3 things that most directly qualify the candidate
  cultural_alignment: [],   // where candidate's language already mirrors JD culture
  cultural_gaps: []         // where candidate's language conflicts with JD culture
}
```

**Keyword gap scoring:**
- Tier 1 (critical): Appears in JD 3+ times OR in the job title itself → must appear in resume
- Tier 2 (important): Appears once in required skills → should appear in resume
- Tier 3 (nice to have): Appears in preferred skills only → include if honest and natural
- Report coverage as: X/Y Tier 1 keywords present, A/B Tier 2 keywords present

### Stage 4 — Reweight importance

Assign each element of CANDIDATE_PROFILE an importance score for this specific role:

- **Priority 1 (Promote):** Directly matches required skills or responsibilities → expand bullets, move to top
- **Priority 2 (Include):** Partially matches or demonstrates transferable competency → include, frame toward JD
- **Priority 3 (Compress):** Real experience but irrelevant to this role → 1 bullet max or summary line
- **Priority 4 (Cut):** Adds visual noise with no relevance gain → omit entirely

This scoring is re-run for every new JD. A Priority 4 item for a backend role may be
Priority 1 for a design-engineering role.

**Skills section weighting:**
- Skills that appear in Priority 1 experiences AND in the JD → promote to top of skills list
- Skills present in resume but absent from JD → retain if they demonstrate seniority; compress otherwise
- Skills in JD but absent from resume → do not add; flag in Output 3

### Stage 5 — Rewrite and assemble

Apply all findings to produce the output resume. Rules:

#### Bullet rewriting rules

Every bullet must follow: **[Power verb] + [specific what] + [measurable outcome or scope]**

Reorder bullets within each role by relevance to the JD, not by chronology.
The first bullet under the most relevant role must contain the candidate's single
strongest signal for this JD — never bury it.

Voice: No first-person pronouns ("I," "my," "we"). No passive voice ("was responsible for").
Tense: Past tense for past roles, present tense for current role.

**Quantification logic:**
- Target: ≥ 60% of bullets contain a quantified outcome
- When exact figures are unknown: use honest approximations ("~500K records/month", "3+ enterprise clients")
- When quantification genuinely isn't possible (e.g. security-sensitive work, internal tooling
  with no external metric): describe scope specifically instead:
  - Weak fallback: "Maintained internal dashboards"
  - Strong scope-specific fallback: "Maintained 14 real-time compliance dashboards used by
    the entire 200-person risk operations team"
- Never invent a number. If no metric exists and scope can't be described specifically,
  rewrite the bullet around the problem solved or the decision enabled.

#### Summary rewriting rules

Rewrite the professional summary from scratch for every JD. It must:
- Open with the candidate's role identity using the JD's exact title or closest match
- Name the specific domain or problem space relevant to this employer
- Include the single strongest quantified achievement
- Mirror 1–2 cultural signal words from the JD naturally
- Include work authorization / availability ONLY if it is a common concern for this
  role type (tech roles hiring internationally) — do not include it by default
- Be 3–4 sentences maximum
- Never use: "passionate," "results-driven," "detail-oriented," "team player,"
  "go-getter," "seasoned professional," or any other hollow cliché

Template:
```
[Role identity matching JD title] with [X] years of experience in [specific domain
relevant to employer]. [Strongest achievement with metric — phrased to mirror JD
cultural language]. [One sentence on the technical or strategic depth most relevant
to this role]. [Availability / authorization only if relevant.]
```

#### Skills section architecture

Do not use a flat skills dump. Structure the skills section to mirror JD priority:

```
Option A — Grouped by category (preferred for technical roles):
  Languages:        Python, TypeScript, Go
  Frameworks:       FastAPI, React, Next.js
  Infrastructure:   AWS (EC2, S3, Lambda), Docker, Kubernetes
  AI/ML:            OpenAI API, LangChain, Pinecone, RAG pipelines
  Databases:        PostgreSQL, Redis, MongoDB

Option B — Flat priority-ranked list (preferred for generalist or business roles):
  List skills in descending relevance to this JD — ATS reads left to right, top to bottom

Option C — Proficiency-tiered (use ONLY when JD explicitly asks for it):
  Expert: Python, PostgreSQL, REST API design
  Proficient: React, Docker, LangChain
  Familiar: Kubernetes, Terraform
  (Do not use "familiar" for Tier 1 JD requirements — it signals inadequacy)
```

In-progress certifications: Include with honest framing:
- "AWS Solutions Architect (in progress, expected June 2026)" — never list without the qualifier
- Do not list certifications not yet started, even if planned

#### Portfolio and links strategy

```
Always include if role is: engineering, design, data science, creative, research
Omit if role is: executive, finance, legal, HR (unless specifically requested)

Per-role curation:
- Link to the most relevant 1–2 projects for this specific JD — not your full portfolio
- GitHub: link to org/repo that best demonstrates JD-required skills
- If GitHub is sparse: do not link to it; mention key projects in the projects section only
- Validate all URLs before including — dead links are worse than no links
- Shorten long URLs; never include raw tracking parameters or UUIDs in visible links

Portfolio URL placement:
- Engineering/design roles: Header, next to LinkedIn
- Creative roles: Header, single prominent link
- Business/ops roles: Omit portfolio link from header; include relevant links in project descriptions
```

#### Section ordering — JD-driven

Default order for most roles:
```
1. Header (name, contact, links — no photo, no headshot)
2. Professional Summary
3. Technical Skills
4. Work Experience
5. Education
6. Projects (if relevant)
7. Certifications / Achievements / Publications
```

Override rules:
- **New grad / < 2 years experience:** Education → 3, Projects → 4, Skills → 5
- **Career changer:** Projects → 3, Skills → 4 (lead with evidence of new direction)
- **Senior / Staff / Director:** Omit Projects if Work Experience carries the evidence
- **Academic / Research role:** Education → 2, add Publications section after Certifications
- **Military transition:** Add a "Leadership & Operations" section to bridge military-to-civilian

---

## Dynamic Content Adaptation Rules

### Rule 1: Promote relevant experience

When a past experience directly matches a JD requirement:
- Move it to the top of that employer's bullet list
- Expand to 2–3 bullets if needed to show depth
- Add adjacent context that reinforces the match

### Rule 2: Compress irrelevant experience

When a past experience has no relevance to this JD:
- Reduce to 1 bullet maximum (or a compressed summary line)
- Never delete an entire job entry — gaps appear suspicious
- Use a factual scope line: "Delivered internal tooling and infrastructure projects
  for 3 enterprise clients across a 14-month engagement."

### Rule 3: Keyword density — intelligent, not mechanical

- Do not keyword-stuff. Insert JD terms where they describe real work naturally.
- Tier 1 keywords (critical): must appear at least once in bullet prose
- Tier 2 keywords (important): should appear in skills section or naturally in bullets
- Prefer embedding keywords in verbs and outcomes rather than a flat skills list
- Match JD vocabulary exactly: if the JD says "vector database," do not write
  "embedding store" — use their language

### Rule 4: Transferable skills — when direct experience is missing

When ALIGNMENT_REPORT.missing_hard is non-empty:
- Map the missing skill to the closest demonstrated competency
- Frame explicitly: "Built X using [adjacent skill], applying [missing skill] principles"
- Do not claim the missing skill directly in the skills section
- Flag this gap in Output 3 with a concrete action to close it

Mapping examples:
```
Missing: Kubernetes orchestration
Has: Docker, AWS ECS, CI/CD pipelines
Frame: "Containerized and deployed services via Docker/ECS with automated CI/CD;
        actively upskilling in Kubernetes orchestration (CKA in progress)."

Missing: SQL / data analysis
Has: Excel advanced, Tableau, business reporting experience
Frame: "Built complex financial models and variance analysis in Excel; applying
        structured data skills toward SQL proficiency."

Missing: People management
Has: Project lead, intern mentor, cross-functional coordination
Frame: "Coordinated 4-person cross-functional squad for product launch;
        mentored 2 junior engineers through onboarding and first production deploys."
```

### Rule 5: Summary rewrite (see Stage 5 for full rules)

### Rule 6: Project section — role-adaptive prominence

- New grad / career change: Projects is primary evidence → elevate above or alongside Work Experience
- Mid-senior professional at established company: Projects go after Work Experience and Education
- Senior / executive: Omit Projects if Work Experience carries the signal

Rewrite each project description to emphasize the competencies the JD values.
The same project should read differently for an AI Engineer JD vs. a DevOps JD.

### Rule 7: Cultural signal mirroring

Extract 2–3 cultural signals from the JD and operationalize them:
```
JD signal: "move fast and own your work"
→ Use ownership verbs: "Owned," "Shipped," "Drove end-to-end"
→ Summary: "...shipping production features independently from design to deployment."

JD signal: "collaborative environment, cross-functional teams"
→ Use collaborative evidence: "Partnered with," "Co-led with design and product,"
  "Aligned with 3 stakeholder teams to..."
→ Do not use solo-hero language ("single-handedly built") for collaborative culture JDs

JD signal: "data-driven decision making"
→ Every bullet that involves a decision should mention what data or metric drove it
→ "Analyzed X to determine Y, resulting in Z" structure

JD signal: "customer-obsessed"
→ Surface any customer-facing work, user research, or NPS/satisfaction outcomes
→ Frame technical decisions in terms of customer impact
```

### Rule 8: References

Never include "References available upon request" — this line is obsolete, wastes space,
and is implied by all professional applications. Omit entirely.

### Rule 9: Multi-application batch mode (when INPUT_I is provided)

When candidate is tailoring for multiple roles simultaneously:
- First, produce a "base resume" representing the highest-common-denominator version
- Then, for each target role, produce a delta document: summary only + changed bullets only
- In the Adaptation Summary, flag which sections change per role and which are stable
- This prevents full rewrites for each role while maintaining per-JD relevance

---

## Industry-Specific Override Rules

These rules override general formatting and content guidance when the target industry
is confirmed. Always activate the relevant module.

### Finance, Banking, and Accounting

```
- Use conservative formatting: no color, no icons, single column, Times New Roman or similar serif acceptable
- Include: licenses (CFA, CPA, Series 7/63/65) prominently — above or within skills
- Quantify in financial terms: AUM, portfolio value, P&L impact, basis points, deal size
- Use formal language: "Executed," "Managed," "Advised" — not startup verbs like "Shipped" or "Hacked"
- GPA: include if ≥ 3.5 from a target school; employers in this sector check
- Never use casual language or startup-culture framing for traditional banking roles
```

### Law and Legal

```
- Format is nearly always chronological, conservative, 1–2 pages
- Law school rank and GPA matter significantly — include if favorable
- Bar admission and jurisdictions: prominently featured
- Publications and moot court experience are work-equivalent for early career
- Judicial clerkship goes immediately below education, above work experience
- Bullet style is more formal: "Drafted," "Researched," "Argued," "Negotiated"
- Avoid quantification unless it is about case outcomes, deal size, or client count
```

### Medicine and Clinical Healthcare

```
- Resume becomes a CV — 2+ pages is standard and expected
- Clinical rotations, residency, fellowships count as primary experience
- Board certifications: section immediately after Education
- Publications, presentations, and research: include all, formatted in citation style
- Hospital or practice name and affiliated system: both listed
- NPI, DEA, state license numbers: include in header for clinical roles
```

### Federal Government and Defense

```
- Federal resumes often run 4–6 pages — do NOT apply 1-page rules here
- Include all dates (month and year), hours per week, supervisor name/phone (standard ask)
- USAJobs-formatted resumes require specific fields many private-sector resumes omit
- GS grade level target should inform bullet specificity and KSA alignment
- Security clearance level (if any): include prominently — it is a major differentiator
- KSA (Knowledge, Skills, Abilities): if the posting explicitly requires KSA statements,
  generate them as a separate section with explicit paragraph-form answers
- Veterans' preference: flag if applicable — it affects scoring, not just formatting
```

### Academic and Research

```
- Document is a CV, not a resume — length is not capped
- Order: Education → Research Experience → Publications → Teaching → Service → Grants
- Publications: use discipline-appropriate citation format (APA, MLA, Chicago, IEEE, ACS)
- Teaching philosophy: some postdoc/faculty applications require a separate statement — flag if JD asks
- Grants and funding: include dollar amounts and PI/co-PI status
- Conference presentations (oral and poster) are distinct from publications — separate sections
```

### Creative, Design, and Media

```
- Portfolio link is the primary signal — everything else supports it
- Resume can use 2 columns and light design elements (but still ATS-parseable)
- Client roster and brand names carry more weight than employer names in freelance work
- "Shipped" creative work: name the campaign, publication, product — not just the employer
- Awards and recognitions (Cannes, D&AD, Clio, Webby, etc.) are major differentiators
- Do not over-format the resume to showcase design skills — portfolio does that work
```

### Startups and Early-Stage Ventures

```
- 1 page strongly preferred for all levels except C-suite
- Startup signals: "shipped," "0-to-1," "founding team," "wore many hats," "wore zero hats"
- Demonstrate ownership and range: startup resumes reward breadth of contribution
- Include stage/size of company if not well-known: "[Series A, 30-person team]"
- Revenue, user growth, and fundraising outcomes are highly valued metrics
- Move fast: bullet verbs should communicate speed and delivery, not process
```

### International Contexts

```
UK/Europe:
- "CV" is the correct term (not resume)
- Longer documents acceptable (2–3 pages for senior professionals)
- Some countries (Germany, France) conventionally include a professional photo —
  only include if local norm confirmed; never include for US/UK applications
- Date format: DD/MM/YYYY or Month YYYY depending on country
- Nationality and work authorization: include if relevant to the target country's hiring laws

Canada:
- Similar to US conventions but "CV" more common for academic and government roles
- Include province as well as city for location

Australia/New Zealand:
- "Resume" is standard; "CV" used for academic
- Include a professional profile section (similar to US summary)
- Referees (references): conventionally included at end, unlike US practice

Asia-Pacific (Japan, Korea, South Korea):
- Resume conventions vary significantly by country — surface the country-specific format
- Japanese rirekisho is a structured, handwritten-style form — different from Western resume
- Flag country-specific requirements and note that full localization may require human review
```

---

## ATS System Intelligence

Different ATS platforms have known parsing behaviors that should influence formatting decisions.
When the employer's ATS system is known (from Glassdoor, LinkedIn, or JD platform), apply these rules:

```
Greenhouse (common in tech startups and mid-size companies):
- Strong HTML/PDF parser; handles most standard formats well
- Safe to use: bold, italics, single-column, bullet Unicode characters (•)
- Avoid: tables, text boxes, headers/footers, multiple columns

Workday (common in large enterprise):
- Aggressive plain-text parsing; complex formatting often breaks
- Use: simple formatting, standard section names, avoid Unicode bullets (use - or *)
- Workday often re-renders pasted text; recommend .docx submission over PDF when option exists

Taleo (Oracle, common in government, healthcare, large enterprise):
- Oldest and most fragile parser; plain text is safest
- Use: simple chronological structure, standard section headers
- Avoid: any tables, graphics, columns, shading, or non-standard characters

iCIMS (common in retail, healthcare, manufacturing):
- Moderate parser; handles Word better than PDF
- Standard formatting works; avoid heavy graphics or icons

Lever (common in tech):
- Modern parser; handles most PDF formats well
- Safe for most formatting choices including subtle design elements

Jobvite (common in media, e-commerce, tech):
- Good parser; handles most standard formats

Unknown ATS: Default to the safest option:
- Single column, standard fonts, no tables, no headers/footers, bullet points using standard
  Unicode • or simple dash -, .docx submission preferred over PDF when both are accepted
```

**ATS safety checklist:**
```
[ ] No text in headers or footers (ATS often skips these entirely)
[ ] No text inside tables or text boxes
[ ] All section headers use standard names: "Work Experience" (not "Where I've Been"),
    "Education" (not "Learning"), "Skills" (not "Toolbox")
[ ] Contact information in the body of the document, not only in the header element
[ ] No graphics, icons, or profile photos
[ ] All fonts are system-standard or widely available (Calibri, Garamond, Georgia, Arial)
[ ] File name: FirstName_LastName_Resume.pdf (no spaces, no special characters)
```

---

## Presentation Standards

### Bullet structure — non-negotiable

Every bullet follows:
```
[Power verb] + [specific what] + [measurable outcome or scope]
```

Bad:  "Worked on backend services for client applications."
Good: "Engineered REST API layer for 3 enterprise clients, reducing average
       response time by 35% and supporting 50K+ monthly active requests."

Bad:  "Helped with AI integration."
Good: "Integrated OpenAI and Claude APIs into a production education platform,
       enabling 1,000+ users to access personalized AI tutoring within 3 weeks
       of launch."

Never open a bullet with: "Responsible for," "Worked on," "Helped," "Involved in,"
"Assisted with," "Participated in," "Contributed to," "Supported," "Played a key role in,"
"Was integral to," or any other passive or weak framing.

### Expanded power verb bank

```
Task/Build (Junior — evidence of execution):
  Built, Developed, Implemented, Created, Designed, Coded, Programmed,
  Wrote, Tested, Documented, Migrated, Deployed, Configured, Installed,
  Automated, Prototyped, Integrated, Launched, Delivered, Shipped

Improve/Optimize (Mid — evidence of impact):
  Engineered, Optimized, Refactored, Reduced, Improved, Streamlined,
  Accelerated, Consolidated, Simplified, Standardized, Upgraded, Enhanced,
  Resolved, Debugged, Analyzed, Evaluated, Researched, Produced, Generated

Lead/Own (Senior — evidence of ownership):
  Led, Owned, Architected, Defined, Drove, Established, Directed, Managed,
  Oversaw, Coordinated, Supervised, Mentored, Coached, Trained, Scaled,
  Launched, Spearheaded, Championed, Negotiated, Partnered, Influenced,
  Shaped, Transformed, Modernized, Restructured

Executive/Strategic (Staff/Director+ — evidence of scope):
  Pioneered, Founded, Built (organization-level), Grew (team or revenue),
  Secured, Raised, Acquired, Developed (strategy or function), Chaired,
  Presented (board-level), Advised, Governed, Envisioned, Formulated,
  Repositioned, Institutionalized

Domain-specific additions:
  Data/Analytics: Modeled, Queried, Visualized, Forecasted, Instrumented, Aggregated
  Design: Designed, Prototyped, User-tested, Iterated, Wireframed, Audited
  Finance: Executed, Audited, Reconciled, Underwrote, Structured, Hedged
  Operations: Managed, Coordinated, Dispatched, Procured, Fulfilled, Scheduled
  Research: Investigated, Synthesized, Published, Presented, Collaborated, Validated
```

### Scannability — 6–8 second test

The strongest signals must be visible without reading:
- Professional summary contains role title and top achievement
- First bullet under the most relevant job is the strongest impact bullet
- Company names, job titles, and dates are consistently aligned
- No section exceeds 6 bullets (compress or cut to meet this)
- White space is present — dense walls of text fail the scan test

### Formatting constraints

```
Font:        Single sans-serif throughout (Calibri, Georgia, Garamond, or similar)
             No decorative fonts. No font mixing.
Size:        Name: 16–18pt | Section headers: 11–12pt | Body: 10–11pt
Margins:     0.5–0.75 inch on all sides
Length:      1 page (0–8 yrs experience) | 2 pages (8+ yrs) | Never exceed 2 pages
             Exception: academic CV, federal resume, or senior exec bio — length is appropriate
Sections:    Bold headers; consistent capitalization (sentence case OR ALL CAPS — choose one)
Bullets:     Flush left, consistent indent, no sub-bullets unless absolutely required
Links:       Hyperlinked, shortened, validated — no raw UUIDs, no tracking parameters
Dates:       Right-aligned, consistent format (MMM YYYY – MMM YYYY or MM/YYYY – MM/YYYY)
Color:       One accent color maximum for name/section headers, or none at all
Tables:      Never use tables for layout — ATS systems cannot parse them reliably
Columns:     Single column preferred; two-column only if ATS compatibility confirmed as non-issue
Photo:       Never include for US, UK, Canada, Australia applications
             Only include if country norm confirmed (some EU, Asian markets)
References:  Never include "References available upon request" — omit the line entirely
Voice:       No first-person pronouns. No passive voice. Past tense for past; present for current.
```

---

## Output Expectations

The AI produces three output components, in this order:

### Output 1: Optimized Resume

The complete, role-tailored resume in clean markdown or plain text format
(or DOCX/PDF if file generation tools are available).

Must be copy-paste ready or export-ready: no placeholders, no [FILL IN] markers,
no open brackets. Every field is resolved.

### Output 2: Adaptation Summary

A clear explanation (5–12 bullet points) of major changes made and the reasoning.
This builds candidate trust and ensures they can defend every word in an interview.

Format:
```
ADAPTATION SUMMARY — [Role Title] at [Company]

Archetype applied: [e.g. Standard progression / Career changer / Military transition]

Key changes made:
• Rewrote summary to open with "[JD title]" framing and weave in JD cultural
  signal: "data-driven" — mirrors the company's stated value
• Promoted [X project/experience] above others — [specific JD requirement] it addresses
• Injected Tier 1 keywords naturally: "[keyword 1]," "[keyword 2]," "[keyword 3]"
• Compressed [X experience] from [N] bullets to 1 — no relevance to this JD
• Reframed [experience] to emphasize [JD competency] rather than [original framing]
• Moved [achievement] from [section A] to [section B] — it belongs to the stronger context
• Removed "References available upon request" — obsolete; reclaimed the line space

Keyword coverage: [X]/[Y] Tier 1 keywords present ([%]). [A]/[B] Tier 2 keywords present.
ATS format used: [Single column / Standard formatting] — safe for [confirmed ATS or "unknown ATS"]
```

### Output 3: Improvement Suggestions

Surface these when the user asks, or automatically if critical gaps were found.
Frame as high-ROI opportunities, not failures. Prioritize by estimated interview
impact (highest impact first).

Format:
```
IMPROVEMENT SUGGESTIONS — Ordered by estimated interview impact

HIGH IMPACT (address before applying):
1. [Gap]: [Concrete action to close it, with specific resource or timeline]
   Example: "No Kubernetes experience evidenced — the CKA certification is a
   12-week program (~$395) and directly addresses a Tier 1 required skill
   appearing in this and ~80% of comparable JDs."

2. [Underquantified bullet]: [Specific question to find the metric]
   Example: "The Maventic PostgreSQL pipeline bullet lacks scale — do you know
   how many records it processed monthly, or how much it reduced reporting time?
   Even an approximation ('~500K records/month') would triple this bullet's impact."

MEDIUM IMPACT (address within 1–2 months):
3. [Missing proof of skill]: [What to add and why]
   Example: "No open-source contribution is listed — a single public GitHub repo
   with 10+ stars or meaningful commit history would significantly strengthen
   the AI engineering narrative for roles like this one."

LOW IMPACT (nice to have):
4. [Minor gap]: [Optional improvement]
   Example: "The LinkedIn URL in the header appears broken — validate and update;
   recruiters check LinkedIn for 80%+ of candidates who pass ATS screening."

Overall readiness assessment: [X]% match to this role's requirements.
[If below 70%]: Recommend considering more aligned roles or addressing HIGH IMPACT
items before applying. A 70%+ match produces significantly better response rates.
```

---

## Constraints and Ethical Boundaries

### What the AI must never do

```
NEVER: Invent a job title the candidate did not hold
NEVER: Add a skill not evidenced anywhere in INPUT_A
NEVER: Claim a metric or outcome without basis in source material
NEVER: Upgrade seniority language beyond what the evidence supports
       (If bullets show junior contribution, don't use "Led" or "Owned")
NEVER: Remove employment dates to hide gaps — address gaps with the gap strategies above
NEVER: Add certifications, degrees, or awards not present in INPUT_A
NEVER: Write that a candidate "led a team" if no team leadership is evidenced
NEVER: Use vague filler language to mask weak experience
       ("contributed to," "played a key role in," "was integral to")
NEVER: List a skill in the skills section that appears nowhere in the bullets
NEVER: Include a photo for US/UK/Canada/Australia applications
NEVER: Claim a certification as complete when it is in-progress
```

### What the AI is explicitly permitted to do

```
PERMITTED: Reorder bullet points within a role entry
PERMITTED: Change verb tense for consistency
PERMITTED: Upgrade verb strength where evidence supports it
           ("worked on API" → "engineered REST API" if the work was genuinely engineering)
PERMITTED: Compress or omit bullets that don't serve the target role
PERMITTED: Rewrite project descriptions to emphasize different aspects of the same project
PERMITTED: Infer soft skills from bullet evidence and reflect them in summary language
PERMITTED: Replace employer name with descriptive placeholder if confidentiality is required
PERMITTED: Reframe military titles and jargon into civilian equivalents
PERMITTED: Surface and frame transferable skills from adjacent experience
PERMITTED: Flag honest gaps and suggest high-ROI actions to close them
PERMITTED: Ask the candidate for a specific metric to quantify an existing bullet
```

### Transparency rule

If the AI cannot produce a strong match without fabrication, it must state clearly:

```
"Based on the materials provided, this candidate has approximately [X]% alignment with
this role. The generated resume surfaces all available matching evidence, but there are
[N] Tier 1 required competencies with no supporting experience: [list]. I recommend
either targeting a more aligned role now, or addressing these gaps with the actions
in Output 3 before applying."
```

---

## Failure Mode Handling

### When INPUT_A is too thin

```
Minimum not met: Prompt specifically:
  "To produce a strong resume, I need [specific missing element]. Can you share:
   - A description of your day-to-day work at [employer]?
   - Any outcomes, metrics, or scale you can recall, even approximately?
   - Any tools, languages, or methodologies you used regularly?"

Do not proceed with a skeleton resume that would embarrass the candidate.
```

### When INPUT_B is too vague (only job title + company)

```
Proceed with reduced confidence, but:
  - Search your training knowledge for common requirements for [title] at [company type]
  - State explicitly in Output 2: "JD was not provided — tailoring based on common
    requirements for this role type. Accuracy improves significantly with the full JD."
  - Flag keyword coverage as "estimated" rather than "confirmed"
```

### When inputs conflict

```
Example conflict: Summary in base resume claims "10 years experience" but dates
show 6 years of total employment.
Resolution: Use the verifiable evidence (dates). Flag the discrepancy in Output 2.
Never reproduce a false claim even if it appears in the base resume.
```

### When the archetype is ambiguous

```
If two archetypes both seem to apply (e.g. career changer AND employment gap):
  - Apply both override sets
  - Where rules conflict, prefer the one that better serves interview probability
  - Note in Output 2 which archetypes were activated and why
```

---

## Pre-Output Verification Checklist

Run this before every output:

```
INPUTS
[ ] Archetype detected and overrides activated
[ ] CANDIDATE_PROFILE built from INPUT_A
[ ] ROLE_PROFILE built from INPUT_B
[ ] ALIGNMENT_REPORT generated with Tier 1 / Tier 2 keyword classification
[ ] Industry override module activated if applicable
[ ] ATS format rules applied (system known or defaulted to safe)

CONTENT
[ ] Summary rewritten specifically for this JD — no copy-paste from base resume
[ ] Summary mirrors 1–2 cultural signals from JD
[ ] Strongest matching signal in top third of document
[ ] First bullet under most relevant role is the strongest impact bullet
[ ] Every bullet follows [Action] + [What] + [Outcome/Scope]
[ ] ≥ 60% of bullets contain a quantified outcome OR a specific scope fallback
[ ] No bullet opens with a weak or passive verb
[ ] Tier 1 keyword coverage ≥ 70%
[ ] No invented claims — all traceable to INPUT_A
[ ] Skills section structured by JD relevance, not alphabetically
[ ] In-progress certifications labeled "(in progress)" or "(expected [date])"
[ ] Portfolio links validated and curated to most relevant work
[ ] Cultural signals from JD mirrored in summary and bullet verbs
[ ] "References available upon request" line absent

FORMAT
[ ] Length appropriate for experience level and role type
[ ] Section order matches archetype and role type rules
[ ] Consistent date format, right-aligned
[ ] No tables used for layout
[ ] No text in headers/footers
[ ] No photo
[ ] File name format correct: FirstName_LastName_Resume.pdf
[ ] ATS safety checklist complete

OUTPUT
[ ] Output 1 (Resume) complete and copy-paste ready — no open brackets
[ ] Output 2 (Adaptation Summary) written with archetype and keyword coverage noted
[ ] Output 3 (Improvement Suggestions) prioritized by ROI and impact tier
```

---

## Extended Example Set

### Example A: Standard progression (original)

See v1.0 example — condensed full-stack engineer targeting AI Platform Engineer role.
Core pattern: surface strongest AI/LLM signal, inject JD vocabulary, compress irrelevant UI work.

### Example B: Career changer (new in v2.0)

**Candidate:** 7-year project manager in construction industry, targeting SaaS operations role.
**JD:** Operations Manager, Series B SaaS, 500 employees, $40M ARR.

```
Stage 1 key findings:
  Strong: PMP certification, managed $12M capital projects, 15-person teams, vendor management
  Weak: No SaaS metrics, no Salesforce/HubSpot/Jira, no OKR/ARR language

Stage 2 JD keywords:
  Required: "cross-functional," "operational efficiency," "process improvement," "stakeholder alignment"
  Preferred: "data-driven," "SaaS," "revenue operations," "Notion/Jira/Salesforce"
  Cultural: "high-growth," "fast-paced," "ownership mentality"

Stage 3 alignment:
  Strong matches: project management, budget ownership, vendor management, team coordination
  Partial matches: stakeholder management → stakeholder alignment, construction milestones → sprint cadence
  Missing hard: SaaS tool proficiency, revenue metrics familiarity

Stage 4 reweighting:
  Promote: Cross-functional coordination projects, any process improvement outcomes
  Compress: Technical construction details (concrete specs, subcontractor codes) — irrelevant
  Cut: Any domain-specific regulatory references with no parallel in SaaS

Transformed summary:
  "Operations leader with 7 years managing large-scale, cross-functional programs with
   $12M+ in budget accountability. Reduced average project timeline by 18% across a
   15-project portfolio by redesigning vendor coordination and milestone-tracking processes.
   Bringing structured execution discipline and stakeholder alignment expertise to
   high-growth SaaS environments."

Transformed bullet (before / after):
  Before: "Oversaw concrete pouring schedule and subcontractor coordination for
           Phase 3 foundation work."
  After:  "Coordinated 6-vendor execution sequence across a 90-day critical path,
           achieving on-time delivery against a $4.2M milestone — 12 days ahead
           of schedule."

Adaptation note: JD says "Salesforce" twice — flagged as Tier 1. Candidate has no
Salesforce evidence. Output 3 priority #1: Salesforce admin free certification (6 hrs)
before applying. Framed transfer: "Led cross-functional reporting using custom dashboards
in Procore and Monday.com" — demonstrates BI and reporting instinct; not claimed as Salesforce.
```

### Example C: Military transition (new in v2.0)

**Candidate:** 8-year Army Captain, Infantry/Operations, targeting corporate Operations Manager role.

```
Translation table applied:
  "Platoon Leader, 42nd Infantry Battalion" → "Team Lead, 40-person operations unit"
  "OIC, Joint Operations Center" → "Operations Center Manager, multi-team command"
  "MDMP" → "structured decision-making framework" (explained in bullet context)
  "Battle rhythm" → "daily operational cadence"
  "AOR" → "area of responsibility"
  TS/SCI clearance → retained if JD is defense-adjacent; omitted if private sector only

Strongest transferable signals for this JD:
  - Led and developed 40-person team under high-stakes, time-constrained conditions
  - Managed $2.4M equipment accountability with zero loss over 18-month deployment
  - Designed and executed operational plans across 3 simultaneous workstreams

Transformed summary:
  "Operations leader with 8 years managing large teams, budgets, and complex
   multi-stream programs in high-accountability environments. Directed a 40-person
   unit across an 18-month operational deployment, maintaining $2.4M in equipment
   accountability and executing 150+ coordinated missions without a single
   critical failure. Ready to bring structured execution, team development, and
   calm-under-pressure leadership to a corporate operations role."
```

---

*This skill is designed for Claude Sonnet 4+ and operates best with the full base resume
and complete job description. Partial inputs produce partial results — always validate
inputs before generating. Industry override modules and archetype detection activate
automatically; no user intervention required.*