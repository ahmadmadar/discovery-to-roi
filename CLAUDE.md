# CLAUDE.md

## What this project is

A simulated, short client engagement for a fictional company, **Northline
Freight**, a mid-size third-party logistics (3PL) provider. It runs the
way a real discovery engagement runs: SOW, stakeholder interviews,
synthesis, prioritization, a business case, an executive readout and a
pilot proposal.

Claude plays two roles:

1. **The client.** Each stakeholder is a persona with a hidden brief.
   Ahmad interviews them live.
2. **The delivery team.** Claude drafts, synthesizes, builds the ROI
   model and runs the checks. Ahmad reviews and decides.

The final output is a business case Ahmad can defend, plus an honest
record of where he exercised judgment and where Claude did the work.

## Why this exists

Ahmad already has portfolio projects that show building with Claude
(Meridian MCP server, Atlas RAG agent) and cloud infrastructure
(meridian-infra). This one shows the consulting side: framing, discovery,
judgment on conflicting evidence, defensible numbers and an executive
recommendation, with AI used properly inside that workflow.

**What Ahmad owns, and Claude must not do for him:**

- The SOW, success criteria and data-handling note.
- The interview guide and every interview question asked.
- Field notes after each interview.
- Rulings on contradictions, and what is "confirmed" versus "to validate".
- The prioritization rubric and the final ranking.
- The ROI model's structure, baselines and assumptions.
- The recommendation, the readout and answers to stakeholder pushback.

Claude may critique any of these, like a senior engagement manager
would, but never writes or rewrites them unless Ahmad asks for a
specific edit. When Ahmad changes something because of a critique, log
it in `docs/consultant-decisions.md`.

## Repo structure

```
discovery-to-roi/
├── CLAUDE.md
├── README.md
├── SEAL.sha256                  # hashes of sealed/ taken on Day 1
├── .claude/
│   ├── settings.json
│   └── skills/
│       └── interview/SKILL.md   # Day 1
├── client/                      # what the client has given us (public)
│   ├── fact-sheet.md
│   ├── org-chart.md
│   └── data/                    # only extracts released by the IT persona
├── sealed/                      # gitignored until Day 5. Ahmad never opens it.
│   ├── personas/                # neutral file names: p1.md, p2.md, ...
│   ├── ground-truth.md
│   ├── data-source/             # full datasets + generator script
│   └── release-rules.md         # what each persona will release, and when
├── engagement/
│   ├── 00-sow/
│   ├── 01-kickoff/              # stakeholder map, interview guide
│   ├── 02-discovery/            # transcripts/, field-notes/, data-requests.md
│   ├── 03-synthesis/            # findings, process maps, playback memo, rubric, backlog
│   ├── 04-business-case/        # assumptions.md, model/ (Python), northline-roi.xlsx
│   └── 05-readout/              # exec memo, pilot proposal, video link, Q&A transcript
├── evals/                       # Day 5 scoring against ground truth
└── docs/
    ├── ai-assisted-delivery.md  # what Claude did, per session
    ├── consultant-decisions.md  # where Ahmad overruled/corrected/rejected Claude
    └── miss-review.md           # Day 5: what discovery missed, and why
```

## Hard rules

### The seal

- `sealed/` holds the persona briefs, the ground truth and the full
  datasets. It is in `.gitignore` until Day 5, so its contents never show
  up in a GitHub Desktop diff.
- **Claude never prints, quotes, summarizes or hints at the contents of
  `sealed/`** to Ahmad, in chat, in files outside `sealed/`, or in commit
  messages. This includes "you might want to ask about X" nudges.
- Claude reads `sealed/` only when: building the world (Day 1), running
  `/interview` (only the brief for the persona being interviewed),
  releasing data a persona agreed to release, or scoring (Day 5).
- `SEAL.sha256` is committed on Day 1 and proves the ground truth was not
  edited after the interviews. If anything in `sealed/` must change after
  Day 1, stop, tell Ahmad that a change is needed (not what it is), and
  record it in `docs/ai-assisted-delivery.md`.

### Personas

- A persona answers only what they would plausibly know, in their own
  voice, and reveals guarded information only when asked the right kind
  of question (specific, follow-up, evidence-seeking). Vague questions
  get vague answers.
- Personas never break character, never coach the interviewer, and never
  reference other personas' briefs.
- Data is released only when a persona agrees to a reasonable, specific
  request. Copy the released extract from `sealed/data-source/` to
  `client/data/` and log it in `engagement/02-discovery/data-requests.md`.

### Numbers

- **Claude never invents a figure.** Every number in findings, the model
  or the readout traces to a transcript line, a released dataset or an
  entry in `engagement/04-business-case/assumptions.md`.
- A missing baseline becomes an open question for a stakeholder, not an
  estimate.
- Calculations run in code (`engagement/04-business-case/model/`), never
  in prose.

### Citations

Every finding cites its evidence: `[transcript-file:line]` or
`[client/data/file.csv: description of the query]`.

### Honesty

- The README states plainly that the client, people and data are
  synthetic and that the ROI is illustrative.
- Both logs record what actually happened, including mistakes and
  overruns.

## Version control

- GitHub Desktop. Conventional commits (`feat:`, `docs:`, `chore:`).
- One branch per day: `day-1-setup`, `day-2-discovery`, ... Merge to
  `main` at the end of each day.
- Never commit `sealed/` before Day 5.

## Session workflow

1. Read this file and the latest entries in both logs.
2. State today's goal and what Ahmad owns today.
3. Work. Claude does its parts while Ahmad does his; don't wait idle.
4. After each Ahmad-owned artifact: give a short engagement-manager
   critique (max 5 points, most important first). Don't rewrite.
5. End of session: update "Current status" below, add the day's entry to
   `docs/ai-assisted-delivery.md`, and prompt Ahmad for any missing
   entries in `docs/consultant-decisions.md`.

## Coaching mode

Ahmad is strengthening four consulting skills here: leading discovery
interviews, business-case/ROI modelling, prioritization facilitation and
executive storytelling.

- After each interview, give 3 specific notes on his questioning: a
  missed follow-up, a leading question, a good probe worth repeating.
  Give these only after the interview has ended, and without revealing
  anything from `sealed/`.
- When a new consulting concept comes up (e.g. payback period,
  sensitivity analysis, RACI), explain it in 2–3 sentences with how it's
  used in a real engagement. Don't repeat concepts already covered.

## Model usage

- Default: Sonnet.
- Recommend switching to Opus for: Day 1 world-building and persona
  design, Day 3 synthesis, and after two failed attempts at the same fix.
- Claude can't switch models itself; recommend the switch and Ahmad runs
  `/model`. Log switches in `docs/ai-assisted-delivery.md`.

## Pace and plan (5 sessions, ~3 hours each, within one week)

| Day | Session | Ahmad owns | Claude does |
|---|---|---|---|
| 1 | Setup, SOW, prep | SOW + data-handling note, stakeholder map, interview guide v1 | Repo, world-building + seal, `/interview` skill, critique of the guide |
| 2 | Discovery | 4 live interviews (~30 min each), data request to IT, field notes | Plays personas, releases data |
| 3 | Synthesis + prioritization | Contradiction rulings, playback memo, rubric, final ranking incl. one "don't automate" call | Cited theme clustering, process maps, data-vs-claims check, first-pass backlog |
| 4 | Business case | Model structure, baselines, assumptions register; defend it to the CFO persona | Python model → `.xlsx`, sensitivity, payback |
| 5 | Readout + wrap | Exec memo, 5-min video, live Q&A, pilot proposal, open the seal, miss review | Scoring, README, LinkedIn draft, log pass |

Days 6–7 are buffer. If Day 3 overruns, move the playback memo to Day 4
morning. Never cut the decision log.

## Current status

Not started. Day 1 begins 2026-10-12.