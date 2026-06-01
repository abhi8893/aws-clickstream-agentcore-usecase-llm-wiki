# Pre-requisite Context About `llm-wiki` 

What this section contains? 
    - This is the context on the project's research methodology workflow using llms.

What this section is NOT about?
    - This section is NOT about the core research topic i.e. "AWS architecture for 

## Karpathy's original idea

Read `.local/redefine/karpathy_llm_wiki.md` to know what `llm-wiki` is.

## This project's structure

This project's structure is a little bit different. 

### What was kept from the original?

The core architecture follows Karpathy's design faithfully:

- **Three-layer architecture**: Raw Sources (immutable) → Wiki (LLM-generated) → Schema (rules & config)
- **Three core operations**: Ingest, Query, Lint
- **index.md** as the content catalog and LLM navigation entry point
- **log.md** as the chronological operation record with parseable format
- **[[wikilink]]** syntax for cross-references
- **YAML frontmatter** on every wiki page
- **Obsidian compatibility** — the wiki directory works as an Obsidian vault
- **Human curates, LLM maintains** — the fundamental role division

### What new changes were added?

#### Purpose.md — The Wiki's Soul

The original has Schema (how the wiki works) but no formal place for **why** the wiki exists. We added `purpose.md`:
- Defines goals, key questions, research scope, evolving thesis
- LLM reads it during every ingest and query for context
- LLM can suggest updates based on usage patterns
- Different from schema — schema is structural rules, purpose is directional intent

#### Two-Step Chain-of-Thought Ingest

The original describes a single-step ingest where the LLM reads and writes simultaneously. We split it into **two sequential LLM calls** for significantly better quality:

```
Step 1 (Analysis): LLM reads source → structured analysis
  - Key entities, concepts, arguments
  - Connections to existing wiki content
  - Contradictions & tensions with existing knowledge
  - Recommendations for wiki structure

Step 2 (Generation): LLM takes analysis → generates wiki files
  - Source summary with frontmatter (type, title, sources[])
  - Entity pages, concept pages with cross-references
  - Updated index.md, log.md, overview.md
  - Review items for human judgment
  - Search queries for Deep Research
```

### Current Template

- The current folder structure is a template structure for a "research project". 
- This does NOT suit my current usecase, where I have to document, and architect guidance documents for AWS build.
- The folder structure of `raw`, `wiki`, `purpose.md`, `schema.md` etc will need to be adapted for my usecase, as described below


# Goal

Your main goal in this session is to alter the `llm-wiki` folder structure, `purpose.md`, `schema.md` etc files to help me architect the following usecase:

```
A research knowledge base + learning reference to create reference architecture for architecting a full stack web application solution for tracking engagement metrics (clickstream ingestion + aggregation) and correlate them with Agent Performance observability metrics for the chatbot (AWS AgentCore) deployed on the website.
```

# Usecase Context

Read `01-raw/brain-dump/usecase-context/usecase_context.md`


# Scope

Outputs:

- Inline citations + references
- AWS Architecture diagrams + guidance 
- Detailed guidance documents
  - How to Guides
  - Knowledge documents
  - ...
  - etc
  - <!-- Add more -->
- Data Pipeline Design Docs (using a structured n-step framework)
- Concept Glossary with description on various topics
  - AWS
  - Data Engineering
  - Clickstream
  - Google Tag Manager
  - ...
  - etc
  - <!-- Will be refined as a live document -->
- Mermaid diagrams to create flows
- Data engineering pipeline documentation a proper N-step framework
- Indicative "code snippets" to illustrate concepts. 



# How to Alter `llm-wiki` structure?

## GOAL

- Create a purpose built `llm-wiki` workflow for creating the wiki for my usecase. 
- The workflow SHOULD BE GENERIC enough and NOT rigid for my specific usecase
- The workflow should be suited for "Creating design docs + architecture guidance for software applications"
- DO NOT do any detailed research for this task. 


## What needs to be done to create the workflow?

(The granular steps are defined later. Do not execute them yet.)

- Create `raw`, `processed`, `wiki` folder structure suited for the usecase
- Create purpose built `purpose.md`, `schema.md` files
- Examine if defining custom slash commands for claude / agentic harnesses is needed to make the workflow easier and seamless. For e.g.
  - `/workflow-improve`: User gives feedback on improving the workflow
  - `/wiki-ingest`: Ingests the document from `01-raw` with user context
    - User points to a bunch of files
    - Shares some context around it
    - You understand the context, and "register" the entry in raw folder's index.md, log.md
    - You register the entry of the raw file in the index.md as an entry as
      - filepath
      - filetype
      - notes (any notes helpful to ingest the file)
      - tags (this can be iteratively refined; can be a csv separated tags)
      - metadata (any unstructured metadata)
  - `/wiki-process`: Process the unprocessed entries or whichever "raw" file user has pointed to
    - You check the index entry for the raw file(s)
    - Take the notes, metadata into consideration along with any additional user context
    - Use appropriate skills for the relevant filetype to "parse" the file and go through the contents
    - Convert the file into a structured processed document folder in `02-processed`
      - Break down the file's structure into multiple sub sections
      - Create an index.md for each processed document (note that this is different from index.md at root of raw, processed, wiki layers)
      - Create a overview.md for each processed document
        - This should have a frontmatter, brief description and a glossary section
      - Create each section as a separate markdown file with linkages
        - Each section file should have a frontmatter, brief description and a glossary section
      - DO NOT LINK THIS FILE WITH ANY OTHER SOURCE. The idea is to have "almost" 1:1 mapping with raw layer
      - You register the entry of the processed file in the index.md as an entry as
        - filepath
        - filepath_raw
        - description (this can be from the metadata)
        - concepts (add important concepts from glossary)
        - tags (this can be iteratively refined;)
  - `/wiki-finalize`: Finalize the processed files into the final wiki
    - At this point, you now consider the context of the current processed file(s) being ingested with the current state of the wiki
    - You do an efficient search with carefully crafted structure of the wiki document's frontmatter, sections, glossary, concepts, tags, linkages
      - This can be done with custom implementation of keyword searches, grepping keywords etc
    - The structure of the wiki needs to be efficient
      - Decide how each sections should be arranges
      - Decide how the index.md, log.md should be structured
    

  E.g.

  `01-raw`
  ```
  doc_1.pdf
  doc_2.pdf
  index.md
  log.md
  ```

  `01-processed`
  ```
  doc_1/
    index.md
    overview.md
    section-1.md
    section-2.md
    ...

  doc_2/
    index.md
    overview.md
    section-1.md
    section-2.md
    section-2.md
    ...
  ```
  - `/wiki-refine`: Refines the ingested source with specific user feedback (can be present inline in the processed document)
  - `/wiki-lint`: The "linting" workflow as defined in `.local/redefine/example_claude.md` 
  - ...
  - etc
- Finally build a concise `CLAUDE.md`

## What's my ideal workflow?

IMPORTANT: 

  - This is how I envision my workflow to be. 
  - It is is NOT the final "set in stone" workflow. 
  - You should challenge and `grill-me` all across


TLDR: An efficient workflow allows me to ingest any raw document sources, with you (LLM/AI) grilling me interactively with questions to build the ultimate wiki.

My workflow can look something like this

1. I add raw sources in `01-raw` folder
  - websites, pdfs
  - my brain dump markdowns
  - images, architecture diagrams
  - My chats with other llms
  - Deep research output markdowns
  - ...
  - etc
  - Think about how an index, log can be maintained in raw layer
  
2. I ask you to ingest the raw file (raw -> processed)
  - processed layer is meant to be a loose clone of the raw, like a mirror copy of the folder structure - but processed with extracted knowledge.
  - If you need, you evoke skills needed to "parse" the document. For e.g. if it's `pdf`, you invoke the `pdf` skill
  - After parsing, you understand the document, and process in a multi step workflow to create a processed copy in `02-processed`
  - The processed copy should markdown, and should have brief "frontmatter" - so that you can read the top without neededing to read the full file
  - Think about how an index, log can be maintained in processed layer
    - How should the index link which raw sources each processed entry links to?

3. I examine the processed file

  - I can add "specific syntax" feedback inline or give a direct feedback in the chat
  - Think how the "specific syntax" can look like for an ideal workflow
  

4. You incorporate the feedback and ingest it into the final wiki

  - You consider full interlinkages
  - You grill-me with detailed questions
  - After incorporating feedback, you incorporate the document in the final wiki folder
  - Think about how an index, log can be maintained in wiki layer
    - How should the index link which raw sources each processed entry links to?


## Steps

To achieve the current task of creating the starter `llm-wiki` workflow, we can follow the steps:


### Step 1

Understand the current template structure "hardcoded" for typical research workflow

  - Read `purpose.md`
  - Read `schema.md`
  - See folder structure for `raw`, `wiki`

### Step 2

Read the current usecase context

  - Read `01-raw/brain-dump/usecase-context/usecase_context.md`

Now is the time to `grill-me` GRILL ME WITH QUESTIONS! 



Iteratively do the following steps, and keep on grilling me with questions.

### Step 3

Create a refined `purpose.md`


### Step 4

Define `raw` folder structure, schema, index, log etc


### Step 5

Define `processed` folder structure, schema, index, log etc

### Step 6

Define `wiki` folder structure, schema, index, log etc


### Step 7

Create a refined `schema.md`, `purpose.md`

### Step 8 

Build the various slash commands, skills etc as needed for an efficient workflow.
`grill-me` on any questions until we arrive at good slash commands, skills etc
Document the refined workflow at teh end and create a mermaid diagram.

### Step 9

Build `CLAUDE.md`

- Read `.local/redefine/example_claude.md` for inspiration ONLY
- This should have `llm-wiki` worklfow related stuff
  - Anything that's helpful for you to maintain, and help the user with the "ideal" wiki workflow
    - Ideally Step 8's output should have the required info.
  - Should have basic idea of what this wiki is (But not detailed)
  - Detailed reference should always point to `wiki` folder's `overview` etc files



