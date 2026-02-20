# VibeCheck - AI Model Comparison Test

## Models
- Claude (Opus 4.6)
- OpenAI (GPT-5.3 Codex)

## Output Files

| # | Prompt | Claude Output | OpenAI Output |
|---|--------|--------------|---------------|
| 1 | LP Design (和モダン茶室) | outputs/claude/01_lp_design.html | outputs/openai/01_lp_design.html |
| 2 | SVG Animation (ペリカン) | outputs/claude/02_svg_animation.html | outputs/openai/02_svg_animation.html |
| 3 | 3D Game (宇宙船) | outputs/claude/03_3d_game.html | outputs/openai/03_3d_game.html |
| 4 | Creative Writing (47語) | outputs/claude/04_creative_writing.md | outputs/openai/04_creative_writing.md |
| 5 | Philosophy (逆説的真実) | outputs/claude/05_philosophy.md | outputs/openai/05_philosophy.md |

## Fairness Measures
- Identical prompts (same text, no additions)
- No skills/memory/project context referenced
- 1 task per prompt (no multi-turn contamination)
- Separate output directories (no cross-reference)
- Default parameters for both models
