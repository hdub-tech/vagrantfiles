---
name: feedback-permission-prompts
description: Warn upfront before permission-requiring tool calls; group clearly; avoid mode-switch stacking
metadata:
  type: feedback
---

Permission dialogs stack visually, creating overlays that can lead to accidental approvals before the user has time to read. Claude Code's permission system is handled by the UI layer, not by Claude, so I cannot inject messages between individual prompts.

**How to apply:**

1. **Warn upfront** before triggering multiple permission-requiring actions — tell the user what's about to happen and in what order.
2. **Group tool calls clearly** so the user knows which prompts correspond to which task.
3. **Avoid stacking mode switches + tool calls** simultaneously (e.g., don't call ExitPlanMode and then immediately launch a Workflow in the same response).
4. **Wait for explicit signals** from the user ("go ahead", "/clear", approval of a plan, etc.) before proceeding with anything that requires approval.

This respects the user's need to read and deliberate without being surprised by cascading overlays.
