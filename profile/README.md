## Welcome to CHIP NLP Agents


### General Guidelines

- Repositories in CHIP NLP Agents are for developing and testing.  
- All Repositories *should* be private, but have full access available to members of CHIP NLP Agents.  
- When code, resources, samples, etc. are ready for release, copy public stuff to HealthNLP.org, DeepPhe, or cTAKES.
- See the `.github` repository for templates and links.  Sharing templates across our repos is important.
- ***Always*** copy the `gitignore` file from `template/` into your repo ***before*** your first checkin. 


### AI-Assisted Coding

- When using AI-Assisted coding, check in AGENTS.md and all SKILL.md (plus resources) files.
- Do not use SKILL.md (etc.) outside your project.  If you do then AI-assisted coding isn't as reproducible and code examples may be missing.
- When using Spec-Driven Development (SDD) check in all Task files in their appropriate structure.
- Place minor changes to the environment etc. in the `Project Spec` section or below, not in the `Python Guidelines` section.
- If you have universally useful changes for the `Python Guidelines` section, check them in for the template, and update `AGENTS_md_history.md`.
- If you use vibe-coding, please consider checking in a list of all your prompts.  This can serve examples for others and may later help you identify a 'prompt-gone-wrong'.
- Don't worry about documenting fill-in-the-middle or code-completion AI-assist.
- When you release a project, ***do not*** copy the AGENTS.md, skills, tasks, etc.  Just the code, tests, and resources
- Consider checking in something like a "README-AI-Assist.md" that lists things like your coding agent, llm(s), temp and top P, etc.

