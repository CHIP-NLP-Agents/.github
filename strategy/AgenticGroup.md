
# Intro

This document contains some thoughts and general strategies for our projects.  
For more detailed information on tasks, see our Agentic AI Activities [Trello Board](https://trello.com/b/xkqOubJM/agentic-ai-activities).


## Orchestrator

The orchestrator is an A2A Client.  It sends things to A2A Servers.
Ours could also be an A2A Server so that UIs can send it docs and requests through A2A, but REST alone should be fine.
Possible Requests:
- "What is the T stage in ~/docs/pitt/brca/PatientA/doc01.txt"
- "Summarize a treatment timeline for the patient in ~/docs/pitt/brca/PatientA/ and save as json to ~/output/pitt/brca/PatientA/timeline.json"
- "Fully process the pitt cohort in ~/docs/ and save to ~/output/"


## Agent

Each Agent is an A2A Server.  It is reachable by the Orchestrator and other Agents.  We will have AI Agents and non-AI Agents (no LLM) in the workflow.  Obviously we are working on AI Agents, but in a User workflow there is sometimes need to use a non-AI Agent that does the same thing as an AI Agent that we have.  Interfacing will get interesting, but I think that we'll manage.


## Skills

This does not refer to `skills {...}` in an A2A Agent Card.  This refers to `SKILL.md`-defined skill `prompt infills`, resources, assets, tools, etc.    They are particularly useful for -injecting- domain/specialty knowledge into a prompt, for instance CAP protocols for different cancer types.  They can also read references, use tools and run code, so they are extremely powerful.  Skills should be available to all dPhe Agents, so they should all be in a common dir/url.  We will use publicly available skills.  We will also create our own skills as needed per-Agent.  For instance, T stage should probably use a skill that loads references (e.g. CAP protocol) appropriate to the current cancer type.  No python code for prompt injection, etc.  Skills can be used very well with MCP servers.  See [MCP and skill.md Working Together](https://www.agensi.io/learn/mcp-and-skill-md-working-together).


## Gold Mock Agent

Each AI Agent should have a counterpart Mock Agent.  This Mock Agent can be used as a stand-in for an AI Agent when testing a complete workflow for the performance of some AI Agent under development.  This is so that developing and testing an AI Agent isn't horribly slow and resource consuming by the running of other "done" AI Agents in the workflow.  These Mock Agents should read from a file containing a table of train/dev/test Gold data.
The Mock Agent should have ***exactly*** the same A2A AgentCard, just a different implementation.


## Gold Mock Agent Data

A table in a file containing gold annotations for all required Agent input and output.  Mock Agents simply read from this file and return the appropriate content (modes, etc.) for their purpose.


## Communication

Whenever possible, we should use streaming responses from our Agents.  Asynchronous streams may allow some useful control such as task cancelation, async skill launches, instant user feedback, etc.  It may be useful for writing per-doc files while whole-patient processing is ongoing, providing possible input for other async per-doc parts of the workflow, as well as possible "restart" points should a run fail partway through.  
In addition, we should stick to JSON-RPC.  A2A can also use gRPC, but for implementation and debugging json is much easier to deal with. 
It also stays consistent with our use of JSON-RPC for MCP, which (to date) does not support gRPC.  
We should *not* use stdio for anything. 
It is *ok* if you are working day 1 of a prototype, but after that switch to JSON-RPC. 


## Agent Output

I would like to try to use skills (.md) that standardize output from our Agents.  For instance, a skill named "TripleJSON" that has a prompt description and code that takes organized triples and writes them in json.  This could be used by any Agent that is prompted to "output triples in json".  We shouldn't need to create a unique Agent for this, and the ability to use a skill prompt could open doors for things like dynamic headers, field names, etc. that could be easier to handle than through strict MCP tooling.  Skills also allow templates, which may be useful for variable output formats on a single skill, even if we hard-code the template.  For instance json triplet field names for sact in one template, clingen quintuplets in another.  This would be similar to the part of the SACT prompt starting at [prompt line 23](https://github.com/HealthNLPorg/lgt-sact/blob/1023a954bd511d6b998f901b2c52d371f702009a/src/main/resources/prompt/buildSACTTimeline.txt#L23)



# Frameworks

## Workflow

I think that, at the time of writing, CrewAI is handles what we want in a workflow.  It also has a large user and dev base and seems to keep up with trends.


## AI Agents

Individual Agents can use LangGraph, but it isn't necessary.  SACT benefits a little because it uses mention text span extraction and timex normalization in addition to the core SACT AI function.  However, ClinGen could get away with a more monolithic implementation, essentially just the code around the model and prompt.  Output of info could be handled by a skill triggered by the prompt.  e.g. [prompt line 5](https://github.com/HealthNLPorg/clingen/blob/22b1c4568468061c83a6a3f151d9a41ab0b2f79d/src/main/resources/prompt/findClinGen.txt#L5) but from the user/orchestrator.  Imagine being able to swap json for markdown for xml output based upon the user's specification.



# Steps Moving Forward

- The 2 released Agents (SACT, ClinGen) are archived.  They each work independently, but they are not good for an integrated workflow.
- Segments of code can be plucked from the 2 released Agents, I have added links below.
- We are now staying away from PBJ and ctakes altogether.  We are not going to have any ctakes type system reliance in our Agents.
- As we work, use repositories in the github organization [CHIP NLP Agents](https://github.com/CHIP-NLP-Agents).


## [TimeNorm](https://github.com/HealthNLPorg/hnlp-timenorm)

1. Create an MCP Server for [TimeNorm](https://github.com/HealthNLPorg/hnlp-timenorm).
2. Create a SKILL.md for TimeNorm, placing constraints, maybe handling doc time.
- A workflow may or may not require normalization.  e.g. "Create a timeline, output raw findings" vs. "Create a normalized timeline, output as ISO 8601".


## [Time ISO-to-Text](https://github.com/HealthNLPorg/lgt-sact/blob/master/lg-timelines-j/src/main/java/org/healthnlp/lg/timelines/TimeNormalizationRunner.java)

1. Create an MCP Server for ISO-to-text.  TimeNorm provides an ISO normalization, but we have code that will take that and generate human-readable text.  It also calls TimeNorm, but it is nice to have both available as tools.
2. Create a SKILL.md for the ISO-to-text.  It can do things like trim calls to only INSTANT timexes, ignoring DURATION and FREQUENCY.
- A workflow may or may not require human-readable normalization.  e.g. "Put together a normalized timeline, output as plain text".


## [Text Offset Finder](https://github.com/HealthNLPorg/clingen/blob/main/pbj-llm-tools-py/src/pbj_llm_tools/text_offset_finder.py)

1. Create an MCP Server for finding spans in text.  A wrapper for the main code, plus usage call e.g. [get_span_matches](https://github.com/HealthNLPorg/clingen/blob/22b1c4568468061c83a6a3f151d9a41ab0b2f79d/pbj-llm-tools-py/src/pbj_llm_tools/cas_mention_creator.py#L81).
2. Create a SKILL.md for the text offset finder.
- A workflow may not require spans.  Consider previous SACT outputs (spanless) vs. the output for LabelStudio pre-annotations.


## [LGT-SACT](https://github.com/HealthNLPorg/lgt-sact)

1. Create an A2A Server for the SACT prompt and core code.  This would be a wrapper around [lgt_llm_node](https://github.com/HealthNLPorg/lgt-sact/blob/master/lg-timelines-py/src/lg_timelines/lgt_llm_node.py).
2. Create a SKILL.md that can produce json output such as SACT requires.  Do it in a generic manner if possible - e.g. receive key names, order.
3. After creating a skill for json array output, try to do the same for markdown and xml.  This is part exercise, part useful in the future.
4. Modify the SACT prompt.  Pull out the essentials and try to get skills to handle text offsets, time normalization, and file output.
5. Create a tool and skill that can summarize/perform cheap closure on the tlinks.  We've done this 2 ways, implementation is pretty simple.
6. Create a tool and skill that can turn (close) n>=1 triplets into quintuplets with normalized time spans.  e.g. "SACT After 2001 Overlap 2002".
- Normalized, human-readable Time spans are required by the viz tool.


## [ClinGen](https://github.com/HealthNLPorg/clingen)

1. Create an A2A Server for the ClinGen prompt and core code.  This would be a wrapper around [clingen_llm_node](https://github.com/HealthNLPorg/clingen/blob/main/clingen-py/src/clingen/clingen_llm_node.py).
2. Create a SKILL.md that can produce json output such as ClinGen requires.  Do it in a generic manner if possible - e.g. receive key names, count.
3. After creating a skill for json array output, try to do the same for markdown and xml.  This is part exercise, part useful in the future.
4. Modify the ClinGen prompt.  Pull out the essentials and try to get skills to handle file output.


## [T Stage](https://github.com/CHIP-NLP-Agents/renal_cr_invasion)

1. See pretty much everything above.
2. Create a SKILL.md to handle use of the CAP protocol appropriate to the cancer type.
- It shouldn't be too hard for a skill to load one of several resources created from CAP protocols.  It should not require a new tool.


## Orchestrator

I'll try to work on this.  
We should work on the Agents above without waiting for an orchestrator.  The individual Agents should be set up so that they can be fed docs, patients, etc. via a test harness.


## Ontology

I'll be creating skills and an MCP server for this as soon as I can.  One skill will be term normalization (e.g. SACT synonyms), which is essential for things like putting together a SACT timeline.


## OMOP CDM Database

I'll be creating skills and an MCP server for our Postgres OMOP database.  We will be using this to fetch -bronze- annotations in structured data and documents from the [NOTE table](https://ohdsi.github.io/CommonDataModel/cdm55.html#note) as well as writing to the [NOTE_NLP](https://ohdsi.github.io/CommonDataModel/cdm55.html#note_nlp) and [FACT_RELATIONSHIP](https://ohdsi.github.io/CommonDataModel/cdm55.html#fact_relationship) tables.  
I will probably also be adding a custom PATIENT_NLP table to our db.  I will also add elements missing from NOTE_NLP like episodes to fit the schema of the [OMOP Oncology Extension](https://ohdsi.github.io/CommonDataModel/oncology.html).

