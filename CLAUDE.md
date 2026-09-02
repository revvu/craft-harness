# Craft Harness

## Lavish artifacts use the craft-harness skin

This repo follows the global Lavish-skin convention (see the global CLAUDE.md): set `<html lang="en" data-theme="craft-harness">` and inline the entire contents of `.lavish-shared/lavish-skin.css` (in this repo) into the artifact's `<style>` block.
Inline it — never `<link>` to it — so the artifact stays portable when opened or exported outside Lavish.

- The project is docs-only, so the skin file is itself the brand's source of truth; adjust the brand there, never inside individual artifacts.
- The look is a dark workshop palette: warm charcoal surfaces, brass primary, copper secondary, verdigris accent, with serif headlines (Iowan Old Style/Palatino stack, no webfont to ship).
