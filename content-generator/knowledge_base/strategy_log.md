# Strategy Log

> The orchestrator appends a short summary after every `/run-pipeline` — topics selected, topics discarded, pillars covered, and any preferences you expressed. This is the system's memory of how you work. It starts empty.

---

## Session — 2026-07-22

**Topics selected:** Data science jobs are bifurcating into two archetypes — which one are you building toward?
**Topics discarded:** LLM-adjacent work is a hard filter in senior DS interviews; The product DS interview loop, decoded; Why "product impact, not algorithmic elegance" is the actual rubric; The BA→DA→DS ladder is back in style; CUPED and sequential testing are table stakes; Meta's AI reshuffle and what it signals for DS careers; "Data science is dead" is trending again
**Pillars covered:** Career growth in big tech
**Posts published:** 0 (draft-only setup — dry run not executed, dependencies not installed; post finalized and shown to user)
**Dry run:** N/A (publishing = draft-only)

**Preferences noted this session:**
- Dislikes labeled/meta phrasing like "My honest take:" and "Quick self-check:" — wants opinions and prompts stated plainly, not flagged with a header-style lead-in.
- Wants simpler English overall — flagged one revision as "little complicated english."
- Sensitive to AI-written feel: rejected a version with tidy triads, arrow-list-as-listicle structure, and overly neat transitions ("that was the industry quietly admitting..."); preferred a version with a normal arrow list for the two archetypes but plainer connecting sentences.
- Final approved version reused the arrow-list format for Builder/Translator but dropped the "My honest take" framing in favor of "Here's what I think" — still fairly close to a header phrase, worth watching if flagged again next time.

**Notes:**
- `.claude/agents/researcher.md` still carries placeholder content from a different niche template (India/D2C, 4-pillar AI/Business/Health/Growth structure, mandatory Brand Case Study format). Worked around it this run by substituting the user's real pillars, but the file itself should be re-templated.
- `scripts/publish_post.py` fails — `dotenv` module not installed (`pip install -r requirements.txt` not yet run). Not a blocker since publishing is draft-only, but flag before the user tries LinkedIn auto-publish.
