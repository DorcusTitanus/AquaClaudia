---
name: Canvas sandbox vs. live student data
description: The Canvas course Fabian has API access to is a PII-free sandbox; real-student data lives elsewhere and reaches Fabian only pre-anonymized by Jefe
type: project
originSessionId: 2636958e-a0ac-4986-bf57-aa1349dc4811
---
The Canvas instance reachable via `.env` credentials (course 14158537 MULTI 211, course 10703470 MULTI 100, and similar) is Jefe's **personal sandbox** on canvas.instructure.com (Canvas Free for Teachers). It contains no real students and no FERPA-protected data — safe for unrestricted extraction, scripting, and experimentation.

Real student data (submissions, journals, grades, rosters from Crafton Hills) lives on a separate Canvas instance Fabian has no access to.

**Why:** Jefe maintains a strict boundary between his dev environment (where AI tools operate freely) and live student data (FERPA-protected, never touched by AI without anonymization he performs himself).

**How to apply:**
- Scripts that hit the sandbox Canvas can extract anything without PII concerns.
- If a task asks for real student data (e.g., journals for qualitative coding), assume Jefe will deliver the anonymized corpus — do not offer to write pullers that target live instances, do not ask for live-instance credentials.
- Treat any dataset Jefe hands you as already anonymized unless he says otherwise, but still flag anything that looks like a name, email, or ID slipped through.
