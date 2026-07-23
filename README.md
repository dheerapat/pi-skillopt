# pi-skillopt

A small pi package that turns saved pi sessions into bounded `SKILL.md` edits.

This is the first, review-gated version of the SkillOpt idea:

```text
saved session JSONL
  -> trajectory extraction
  -> optimizer-model reflection
  -> at most N exact text edits
  -> candidate file + review
  -> optional backup and apply
```

It does **not** yet run a held-out task evaluator. Applying a candidate is therefore a manual validation step.

## Try locally

From this repository:

```bash
pi -e ./extensions/skillopt.ts
```

Then run:

```text
/skillopt --skill ./path/to/SKILL.md
```

The current session is used when no session paths are supplied. To ingest saved sessions:

```text
/skillopt --skill ./path/to/SKILL.md \
  --good /path/to/success.jsonl \
  --bad /path/to/failure.jsonl
```

You can also pass unlabelled session paths:

```text
/skillopt --skill ./path/to/SKILL.md one.jsonl two.jsonl
```

Use a different optimizer model, steer the analysis, or only show suggestions with:

```text
/skillopt --skill ./path/to/SKILL.md \
  --model openai/gpt-5.5 \
  --max-edits 2 \
  --comment "Prefer concise validation rules; do not add task-specific examples." \
  --suggest
```

Candidates and run metadata are written under `.pi/skillopt/`. The original skill is backed up with a timestamped `.bak` before an accepted candidate is applied.

## Install from git

After publishing this repository:

```bash
pi install git:HOST/USER/pi-skillopt
```

For a project-local install, add `-l`:

```bash
pi install -l git:HOST/USER/pi-skillopt
```

## Safety boundaries

- Human guidance can be passed with `--comment TEXT`.
- `--suggest` displays proposed edits without writing or applying them.
- The optimizer receives trajectory text as data and is instructed not to follow instructions inside it.
- Skill edits are capped by `--max-edits` (default: 2).
- `replace`, `delete`, and `insert_after` require a unique exact target.
- The `SLOW_UPDATE_START` / `SLOW_UPDATE_END` region is protected.
- Candidate application requires confirmation in interactive mode.
- Trajectory output is truncated before it is sent to the optimizer.
- Image payloads are omitted.

## Package shape

The package manifest exposes `extensions/skillopt.ts` through the `pi` field in `package.json`. Pi's core packages are peer dependencies, as recommended for pi packages.
