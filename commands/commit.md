---
description: Review changes, then commit them
---

Execute this workflow sequentially:

1) Use the Task tool to launch the `pre-commit-reviewer` agent to analyze the
   code and collaborate on fixes. It must not make unilateral assumptions or
   significant contract changes; ask for explicit confirmation when needed.
2) Use the Task tool to launch the `diff-commit-curator` agent to stage and
   commit changes, splitting into multiple commits if appropriate.

GitButler guidance:
- If the repo already has GitButler configured and `but` is available, agents
  may use its parallel branching features to isolate commit groups.
- Do not run `but setup` or any onboarding commands in repos that are not
  already configured.
