# 🩺 PULSE: Agentic Workflow Intelligence for Cross-Functional Teams

***Turning routine project data into manager-ready workflow diagnosis***

PULSE is a Python-based agentic workflow optimization tool that analyzes employee capacity and task/event logs to identify where cross-functional work gets stuck. The system produces a manager-facing report with bottleneck diagnoses and recommendatons for efficient work allocation and AI-optimization opportunities.

This project was selected for publication on Kenyon College’s digital research collection. [Read more](https://digital.kenyon.edu/dh_iphs_391/11/)

---

## Why I Built This

Most project management tools are good at tracking **what needs to be done**. They are less effective at explaining **why work slows down**.

In cross-functional teams, delays often come from hidden system-level patterns:

- overloaded specialists,
- unclear ownership,
- repeated handoffs,
- approval loops,
- vendor dependencies,
- late-stage review gates,
- and tasks assigned to people with poor skill fit or limited capacity.

PULSE explores whether standard operational data (tasks, assignments, employee workload, and event logs) can be used to surface those hidden delays and recommend practical interventions.

---

## What PULSE Does

PULSE takes structured project data and generates a three-part workflow intelligence report:

1. **Task assignment recommendations**  
   Assigns unstarted tasks based on skill fit, availability, workload, and capacity risk.

2. **Bottleneck detection**  
   Analyzes task event logs to detect repeated handoffs, queue delays, approval loops, overloaded roles, and late-stage blockers.

3. **AI-assist recommendations**  
   Identifies tasks where AI could accelerate work, such as drafting, summarization, documentation, planning, or first-pass analysis.

The final output is designed to be understandable and actionable for managers and operators. It translates workflow data into plain-language diagnoses and recommended process fixes.

---

## Project Architecture

PULSE is organized as a lightweight agentic pipeline:

```text
Structured Data
    ↓
Data Loader
    ↓
Resource Allocation Agent
    ↓
AI Opportunity Scout
    ↓
Bottleneck Detector
    ↓
Workflow Recommender
    ↓
Manager-Facing Report
```

### 1. Data Loader

Loads structured project artifacts from CSV and JSON files:

- employees (titles and skillsets),
- availability,
- projects,
- tasks,
- task events.

### 2. Resource Allocation Agent

Recommends task assignments using heuristic scoring based on:

- required skill match,
- available hours,
- current workload,
- capacity risk,
- task urgency.

### 3. AI Opportunity Scout

Flags tasks where AI automation would accelerate work, focusing on tasks where AI can draft, summarize, or prepare materials without autonomously making high-stakes decisions.

### 4. Bottleneck Detector

Computes workflow metrics from event logs, including:

- mean and p90 wait time,
- service time,
- handoff counts,
- repeated role visits,
- overloaded employees,
- role-level congestion,
- critical paths,
- wait-to-service ratios.

### 5. Workflow Recommender

Turns detected workflow patterns into operational recommendations, such as:

- reducing approval loops,
- redistributing work from overloaded specialists,
- clarifying completion criteria,
- adding WIP limits,
- introducing reviewer consolidation,
- using AI copilots for repetitive low-risk work.

### 6. Report Emitter

Produces both structured artifacts and a readable Markdown summary for decision-makers.

---

## Synthetic Dataset

PULSE is evaluated using a synthetic dataset mimicking a mid-sized e-commerce organization.

The dataset includes:

```text
employees.csv
availability.csv
projects.csv
tasks.csv
events.csv
synthetic_retail_dataset.json
```

The synthetic organization includes realistic departments, roles, projects, tasks, availability patterns, and task movement events.

### Data schema

| Table | Example fields |
|---|---|
| `employees` | employee ID, name, department, role, skills, max hours, timezone |
| `availability` | employee ID, date, available hours |
| `projects` | project ID, name, description, deadline, priority |
| `tasks` | task ID, project ID, skill needed, estimated hours, assignee, due date, status |
| `events` | task ID, event type, timestamp, from assignee, to assignee |

The data generation process combines Python-controlled structure with LLM-generated project/task detail. Python controls IDs, timestamps, availability patterns, and event simulation, while the LLM helps generate richer project and task descriptions.

---

## Bottlenecks Embedded for Evaluation

To test whether the system could recover realistic workflow problems, I intentionally encoded six bottleneck patterns into the event logs.

| Bottleneck | Description | Detected? |
|---|---|---|
| Analytics backlog | Overreliance on a small specialist team, creating 24–96 hour queue delays | Yes |
| Creative approval loops | Repeated back-and-forth handoffs and long review waits | Yes |
| Engineering interruptions | Mid-task handoffs, late completions, and blocked work | Yes |
| Vendor-dependent delays | 72–168 hour silence periods in operations/supply-chain work | Yes |
| Late finance/legal reviews | Approval delays and clarification loops near deadlines | Yes |
| Initial task misassignment | Skill mismatch corrected by handoff after 6–24 hours | No |

The MVP successfully identified **5 of 6** encoded bottleneck types. The missed task-misassignment case was the most subtle pattern, suggesting that future versions need stronger assignment-quality metrics or more event volume to detect weak skill-fit signals reliably.

---

## Evaluation Approach

This MVP is evaluated on whether it can produce useful managerial insight from limited operational inputs.

The core evaluation questions are:

1. **Can PULSE uncover workflow bottlenecks from event logs alone?**
2. **Do task assignments respect skill fit and employee capacity?**
3. **Are AI-assist recommendations limited to low-risk, human-reviewable work?**
4. **Can the system translate workflow metrics into plain-language explanations and recommended fixes?**

### Key findings

- The bottleneck detector successfully identified repeated handoff loops, queue delays, late-stage approval gates, vendor delays, and overloaded specialist teams.
- Workload summaries highlighted overloaded individuals and departments, helping explain where delays originated.
- Assignment recommendations generally prioritized better skill fit and available capacity.
- AI recommendations were constrained to low-risk assistance tasks with human review.
- The system struggled with subtle task misassignment, which became a useful limitation for future evaluation.

---

## Example Output

The main output is a human-readable management report.

Start here:

[`reports/human_readable_summary.md`](reports/human_readable_summary.md)

The report includes:

- executive summary,
- workload and overload risks,
- recommended task assignments,
- bottleneck explanations,
- AI-assist opportunities,
- process improvement recommendations.

Example report sections include:

```text
Executive Summary
Per-person workload
Unstarted task routing
AI-assist flags with reviewer guardrails
Detected bottlenecks
Recommended workflow interventions
```

---

## Repository Structure

```text
agentic-project-management/
├── data/                     # Synthetic datasets
├── mvp/                      # Core agent logic
│   ├── orchestrator.py
│   ├── resource_allocation.py
│   ├── ai_opportunity.py
│   ├── bottleneck_detector.py
│   ├── workflow_recommender.py
│   └── data_loader.py
├── reports/                  # Generated outputs
│   ├── human_readable_summary.md
│   ├── workload_report.json
│   ├── bottleneck_report.json
│   ├── assignment_report.json
│   ├── ai_opportunities.json
│   └── bottleneck_map.png
├── synthetic_data_generator.py
├── main.py
└── README.md
```

---

## How to Run

Clone the repository:

```bash
git clone https://github.com/tiffani3ng/agentic-project-management.git
cd agentic-project-management
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Run the pipeline:

```bash
python main.py
```

Generated reports will be saved in the `reports/` directory.

---

## Why This Matters

PULSE is a prototype for turning operational exhaust into actionable workflow intelligence.

The broader idea is that many organizations already collect the data needed to understand where work gets stuck. PULSE shows how structured project artifacts can be used to:

- detect hidden coordination failures,
- improve task allocation,
- reduce bottlenecks,
- identify safe AI automation opportunities,
- and help managers make decisions based on workflow evidence rather than intuition alone.

For AI-enabled organizations, this type of system could support continuous outcome tracking to understand not just if teams are using tools, but whether those tools are improving execution, reducing delays, and helping people work more effectively.

---

## Responsible AI and Ethics

PULSE is designed with several responsible AI constraints:

- **Human-in-the-loop recommendations:** AI suggestions are limited to low-risk, reviewable work.
- **No autonomous high-stakes decisions:** The system recommends actions but does not execute them.
- **Auditability:** Outputs are saved as structured reports so recommendations can be inspected and challenged.

---

## Future Work

This is an MVP, not a production deployment. Several extensions would move PULSE closer to real-world deployment:

- dynamic project and task updating,
- continuous re-evaluation as workflow events occur,
- Google Calendar or Microsoft Outlook integration,
- Slack or Teams bot for day-to-day workflow updates,
- Streamlit or web dashboard for manager review,
- learned task-assignment weights based on outcomes,
- stronger task-misassignment detection,
- role-based access controls,
- audit logs for recommendations and overrides,
- before/after measurement of workflow interventions.

---

## Selected References

This project draws on research in task interruption, bottleneck management, project coordination, and workflow optimization.

- Abad, Z. S. H., Karras, O., Schneider, K., Barker, K., & Bauer, M. (2018). *Task interruption in software development projects: What makes some interruptions more disruptive than others?*
- Karimi, H., Sarvari, H., Edwards, D. J., Chan, D. W. M., & Olawumi, T. O. (2024). *Deploying Bottleneck Management Strategies for Ameliorating Critical Delays in Building Construction Projects.*
- Bull, D. (2025). *Operational Bottlenecks and Workforce Efficiency: A Quantitative Evaluation Using the Theory of Constraints in Healthcare.*

---

## Author

**Tiffanie Ng**  
Economics & Mathematics major, Scientific Computing concentration  
Kenyon College ’26  
[LinkedIn](https://www.linkedin.com/in/tiffanie-ng)
