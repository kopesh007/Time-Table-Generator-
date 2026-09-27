# Timetable Scheduler — GitHub Base-Project Research & Adoption Specification

## 1. Agent Mission

Find the closest working GitHub base project for a college timetable scheduler. **Do not build the whole application from scratch.** Search broadly, inspect actual source code, verify the license, and choose the repository that minimizes rewrite effort while satisfying the college-specific rules below. Treat the candidate repositories in this brief as research leads, not as the final answer.

## 2. Project Definition

A web-based automated college timetable scheduler that collects teachers, classes/sections, subjects, teacher subject preferences, credit/point values, lab requirements, time structures and other rules; then assigns teachers/classes/subjects/labs and generates a timetable that satisfies hard constraints while optimizing dynamically weighted soft constraints.

## 3. Confirmed College Workflow

1. Determine number of teachers.
2. Determine number of classes/sections.
3. Identify subjects.
4. Teachers can select subjects they prefer/want to teach.
5. If multiple teachers select the same subject, use soft constraints to resolve the competition.
6. Teacher-to-class/section assignment may be automated, or preferences may be represented as soft constraints.
7. Credit and point are the same concept for this project.
8. Credit/point values and their influence on weekly hours are dynamically controlled through soft-constraint policy. The exact formula is not yet known and must remain configurable.
9. Labs are allocated using soft constraints plus randomized tie-breaking; multi-hour lab sequences must be contiguous and complete.
10. Generate and validate final timetable.

## 4. Core Domain Model

- Institution / Department
- Academic Year / Semester
- Class / Section
- Teacher
- Subject
- Classroom
- Laboratory
- Time Slot / Day
- Activity / Timetable Entry
- Constraint / Preference / Weight
- TeacherPreference
- TeachingAssignment
- SubjectRequirement
- ResourceRequirement
- Availability
- SoftConstraintRule

Important: support `Class/Section + Subject -> Teacher`, not only `Subject -> Teacher`. Support `Class/Section + Subject -> Weekly Hours`, because the supplied timetable shows section-specific hour differences.

## 5. Target Web Structure

```text
User/Admin
  -> Web UI / Portal
  -> Backend/API
      -> Database
      -> Validation Engine
      -> Scheduler/Solver
  -> Timetable Results
      -> Class View
      -> Teacher View
      -> Room/Lab View
      -> PDF / Excel / CSV
```

Preferred direction: Python/Django where practical, PostgreSQL/MySQL, dedicated scheduler engine. React optional. OR-Tools/CP-SAT desirable; GA/hybrid acceptable if maintainable.

## 6. Functional Requirements

- Academic setup
- Teacher management
- Subject management
- Class/section management
- Teacher subject preference
- Soft resolution of competing teacher preferences
- Teacher + subject + class assignment
- Credit/point management
- Configurable weekly-hour calculation
- Section-specific subject hours
- Room management
- Lab management
- Configurable time slots
- Lab scheduling
- Consecutive multi-period labs
- Hard-constraint validation
- Soft-constraint optimization
- Automatic generation
- Multiple alternatives when supported
- Conflict diagnostics
- Manual override + revalidation
- Timetable views
- PDF/Excel/CSV export
- Excel/CSV import when feasible
- Authentication/roles when needed
- Persistence/versioning when practical

## 7. Hard Constraints

- No teacher double-booking.
- No class/section double-booking.
- No room double-booking.
- No lab double-booking.
- Required weekly coverage must be met according to the configured policy.
- Mandatory teacher availability.
- Mandatory class availability.
- Room availability.
- Lab availability.
- Capacity.
- Facility compatibility.
- Fixed events/breaks/lunch/holidays.
- Consecutive multi-period practicals when configured.
- Exact assignment integrity.

## 8. Soft Constraints

- Teacher subject preference.
- Competition between teachers for a subject.
- Teacher-class preference.
- Credit/point policy.
- Weekly-hour preference policy.
- Lab priority.
- Randomized tie-break among equally feasible options.
- Teacher preferred windows.
- Workload balance.
- Back-to-back fatigue.
- Student/class gaps.
- Room movement.
- Weekly subject distribution.
- Future extensible preferences.

### Soft-constraint design requirement

Soft constraints must be configurable (weight, enabled flag, scope, parameters) rather than embedded in hard-coded logic. Randomness must only choose among already-feasible alternatives. Store a random seed when reproducibility is needed.

## 9. Credit/Point Policy

Credit and point are the same concept. Their value is influenced by soft constraints, and that value affects the subject's weekly coverage. The exact conversion rule is not yet known. **Do not hard-code the relation.** Create a policy layer such as:

```text
credit_point_value
  -> soft-constraint policy
  -> weekly_hours_required
  -> schedulable blocks
  -> timetable placement
```

The system should be able to explain the derived weekly hours.

## 10. Lab Requirements

- Lab allocation is preference-driven.
- Random pick may be used only as a tie-break among feasible equal-score alternatives.
- Multi-hour practicals that require sequence must be contiguous.
- Validate teacher, class, lab, capacity and facility compatibility across the entire block.
- Explain why a required lab block cannot be scheduled.

## 11. Time Structure

Do not hard-code one universal timetable grid. The supplied college timetable contains different time structures across years/sections. Support configurable working days, period count, start/end times, breaks, lunch, holidays and fixed activities.

## 12. Important Domain Patterns from the Supplied Timetable PDF

- Multiple years and sections (e.g. II A/B, III A/B, IV A/B).
- Subject tables contain code, name, credits, hours and faculty.
- Labs are separate from theory.
- Non-theory activities include training, aptitude, Moodle, mentor, library/yoga, Naan Mudhalvan, soft skill and mini project.
- The same subject can have different hours for different sections.

## 13. Open Questions — Do Not Invent

- Exact credit/point-to-hours formula.
- Score range.
- Meaning of high/low score.
- Exact soft factors/weights for competing teachers.
- Exact teacher-to-class scoring.
- Whether assignments can be locked.
- Which soft weights are runtime-configurable.
- Desired randomization/seed behavior.
- Whether every multi-hour lab must be contiguous or only specific labs.
- Which capacity/resource rules are hard vs soft.
- Final roles, exports and integrations.

## 14. GitHub Base-Project Selection Criteria

Suggested weighting:

| Criterion | Weight |
|---|---:|
| Scheduler fit | 25% |
| College-rule fit | 20% |
| Architecture fit | 15% |
| Web-stack fit | 10% |
| Lab support | 10% |
| Data/import/export | 5% |
| Maintainability/tests | 5% |
| License/reuse | 5% |
| Repository health | 5% |

Reject as the primary base if the repo has no workable scheduler engine, cannot model the core entities/resources, has no usable license for the intended reuse, or would require rebuilding most of the scheduler.

## 15. Research Procedure

Search GitHub/web using combinations of:

- timetable generator university college scheduler Python
- Django timetable generator university
- OR-Tools timetable scheduler college
- CP-SAT university timetable
- genetic algorithm timetable Django
- consecutive lab timetable scheduler
- teacher availability timetable generator
- soft constraints timetable scheduler
- Excel timetable generator Django
- academic scheduling Python OR-Tools

For every serious candidate inspect: README, tree, package files, solver, constraints, models, import/export, tests, environment setup, Docker, license, recent activity, issues and PRs. Run where possible.

## 16. Initial Research Leads

- https://github.com/shruteesalpe/Timetable-Generator-Using-AI — React/Flask/PostgreSQL, OR-Tools + GA, hard/soft constraints, consecutive labs, MIT license.
- https://github.com/madhavv-05/Timetable-Scheduler — Django/PostgreSQL, GA + Simulated Annealing, custom constraints, CSV/PDF/Excel.
- https://github.com/GEHU-Opensource/Time-Table — Django backend + React frontend, GA, roles, multi-tenant, MIT license.
- https://github.com/Soumadipta-Konar/Routine-Generator-CP-SAT — Django/DRF/PostgreSQL, OR-Tools, Excel, manual overrides, consecutive practicals, MIT license.
- https://github.com/aldahiiir/timetable — reusable Python + OR-Tools CP-SAT backend engine, explicit constraints and diagnostics, pre-alpha.
- https://github.com/Ashutoshshahi17/timetable-generator — Django + GA + SQLite + PDF; small/older repo requiring careful audit.

These are **leads, not the final selection**.

## 17. Candidate Audit

For every candidate capture:

- Repository URL
- License
- Backend/frontend/database/solver
- Scheduler module
- Hard/soft constraint implementation
- Teacher/class/subject/resource model
- Preference/weight model
- Credit/point support
- Weekly-hour support
- Lab block support
- Randomness/seed
- Validation and diagnostics
- Manual overrides
- Import/export
- Authentication
- Tests
- Setup reproducibility
- Activity/maintenance
- Reuse effort
- Risks

## 18. Target GitHub Architecture

```text
timetable-system/
├── backend/apps/{accounts,academics,scheduling,timetables,imports,exports}/
├── solver/{models,constraints,objectives,policies,engine.py,diagnostics.py}
├── frontend/ (optional separate frontend)
├── templates/
├── static/
├── data/{sample,schemas}/
├── tests/
├── docs/
├── scripts/
├── .github/workflows/
├── .env.example
├── README.md
└── LICENSE
```

Preserve good existing structure; do not force a rewrite just to match this tree.

## 19. Team Git Architecture

```text
main        -> stable
develop     -> integration (optional)
feature/*   -> one change
fix/*       -> bug fixes
solver/*    -> scheduler engine
ui/*        -> UI
data/*      -> models/import/export
```

Keep imported upstream code separate from college-specific changes. Keep scheduler logic isolated from UI code.

## 20. Adoption Plan

1. Verify license.
2. Clone/fork.
3. Run the original system unchanged.
4. Create a fit-gap matrix.
5. Preserve reusable scheduler core.
6. Add college-specific data/policy fields.
7. Separate hard/soft constraints if necessary.
8. Add configurable credit/point policy.
9. Add consecutive labs + seeded randomized tie-break if missing.
10. Add diagnostics and manual override validation.
11. Add college UI/import/export.
12. Test against real timetable patterns.
13. Document all upstream vs college-specific modifications.

## 21. Acceptance Tests

- Teacher conflict blocked.
- Class conflict blocked.
- Subject preference competition resolved by soft weights.
- Teacher-class preference applied.
- Changing credit/point policy changes weekly hours without rewriting solver.
- Same subject can have different hours by section.
- Multi-period lab is contiguous.
- Random lab tie-break never violates feasibility.
- Availability and capacity respected.
- Infeasible workload produces diagnostics.
- Manual override revalidates.
- Non-theory activities are schedulable.
- Optional random seed reproduces results.

## 22. Required Agent Output

Return:
1. Search coverage.
2. Candidate list.
3. Repository audits.
4. Fit-gap matrix.
5. Selected base repository + exact revision inspected.
6. Evidence-based fit explanation.
7. Reuse map.
8. Modification map.
9. New modules required.
10. Risk report.
11. Setup verification.
12. Phased adoption plan.

## Final instruction

The GitHub repository is only the base. The final system must follow this specification and must not silently inherit assumptions that conflict with the college rules.
