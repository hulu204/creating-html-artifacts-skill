# Darwin Evaluation Notes

This file records the reproducible Darwin-style evaluation for `creating-html-artifacts`.

## Test prompts

See `test-prompts.json` in this directory.

## Rubric

The score uses the Darwin Skill 8-dimension rubric:

| Dimension | Weight |
| --- | ---: |
| Frontmatter quality | 8 |
| Workflow clarity | 15 |
| Boundary coverage | 10 |
| Checkpoint design | 7 |
| Instruction specificity | 15 |
| Resource integration | 5 |
| Overall architecture | 15 |
| Practical test behavior | 25 |

## Baseline: local pre-public version

Baseline score: 84.5 / 100

Observed gaps:

- The skill already described when HTML artifacts are useful, but did not make the >500-word design-plan trigger explicit enough for public discovery.
- PDF export was mentioned generically as export/copy behavior, not required as a stable top-right control.
- Auto-open was not part of the workflow.
- Verification did not assert `window.print()`, `.print-button`, or print CSS.
- Public-install expectations were implicit rather than documented as best-effort skill behavior.

## Improved version

Improved score after independent review, auto-open fix, handoff-section expansion, and post-fix Darwin re-evaluation: 97.0 / 100

Independent review found the first auto-open snippet omitted the artifact path argument. The snippet was fixed to use `python3 - "$ARTIFACT_PATH" <<'PY'`, keep argument parsing inside the non-fatal `try` block, and fall back from `xdg-open` to `sensible-browser`. A later handoff-section pass added table-of-contents navigation, an `Original Markdown Plan / 原始方案全文` panel, a `Suggested Approval Phrase / 建议批准语` panel, and copy buttons for both approval text (`复制批准语`) and markdown source (`复制 markdown 原文`). Post-fix Darwin checks verified the previous argv concern is closed and the new handoff controls are mechanically testable.

Main improvements:

1. Added a mandatory long-answer trigger for design, planning, review, report, comparison, and decision tasks over roughly 500 words or Chinese characters.
2. Added a required top-right `Export PDF` button based on `window.print()` plus A4 print CSS.
3. Added best-effort platform auto-open workflow with macOS, Linux, and Windows behavior and a non-fatal fallback.
4. Added verification checks that make PDF export and print CSS mechanically testable.
5. Added reproducible test prompts covering long-plan trigger, architecture-comparison trigger, required handoff sections, and a short-answer control.

## Reproduction command

From the repository root:

```bash
python3 - <<'PY'
from pathlib import Path
import json, re, yaml
p = Path('optional-skills/creative/creating-html-artifacts/SKILL.md')
s = p.read_text(encoding='utf-8')
assert s.startswith('---')
end = s.index('\n---\n', 3)
fm = yaml.safe_load(s[3:end])
assert fm['name'] == 'creating-html-artifacts'
assert len(fm['description']) <= 1024
assert '<!doctype html>' in s
assert 'window.print()' in s
assert '.print-button' in s
assert '@media print' in s
assert 'Auto-open After Generation' in s
assert 'Original Markdown Plan' in s and '原始方案全文' in s
assert 'Suggested Approval Phrase' in s and '建议批准语' in s
assert 'data-copy-target' in s
assert 'Table of contents' in s or 'Contents' in s or '目录' in s
prompts = json.loads(Path('optional-skills/creative/creating-html-artifacts/test-prompts.json').read_text())
assert len(prompts) == 3
assert any('short' in x['id'] for x in prompts)
print('creating-html-artifacts validation OK')
PY
```

## Evaluation mode

`dry_run` plus static reproducibility checks. The skill behavior is prompt-level and depends on the agent loading the skill; hard runtime enforcement would require an opt-in runtime hook or auto-trigger mechanism.
