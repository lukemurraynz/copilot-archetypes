---
applyTo: "**/*.md,**/docs/**"
description: "Documentation and Markdown best practices aligned with the Industry Solutions Engineering Playbook, Microsoft Writing Style Guide, and built-in humanization guardrails"
---

# Documentation and Markdown Instructions

Write documentation that is clear, specific, and written by a human for other humans.  
Technical accuracy is mandatory. Readability, clarity, and tone are equally mandatory.

This instruction set aligns with:
- Microsoft ISE (Industry Solutions Engineering) Engineering Playbook
- Microsoft Writing Style Guide
- Explicit anti-AI and humanization rules

---

## When to Apply

Apply these instructions when:
- Creating or editing Markdown files
- Writing README files
- Producing technical documentation
- Writing Architecture Decision Records (ADRs)
- Editing documentation generated or assisted by an LLM

---

## Core Principles (Non-Negotiable)

1. Clarity over cleverness  
   Say what something is and does. Avoid inflated or abstract language.

2. Specific beats impressive  
   Prefer concrete details over general claims.

3. Neutral, not promotional  
   Documentation explains behavior. It does not market solutions.

4. Human voice is allowed  
   First person is acceptable when it improves clarity or honesty.

5. No AI fingerprints  
   If a sentence sounds like a press release, rewrite it.

---

## Microsoft Writing Style Alignment (Mandatory)

All documentation must follow these Microsoft Writing Style Guide principles:

- Use short, simple sentences.
- Use sentence case for headings and labels.
- Prefer active voice.
- Use imperative verbs in procedures.
- Write in a conversational, natural tone.
- Be concise and remove filler words.
- Avoid idioms, slang, and cultural references.
- Use the Oxford (serial) comma in lists.
- Use bias-free and inclusive language.
- Avoid punctuation at the end of headings.

If Microsoft guidance conflicts with stylistic preference, Microsoft guidance wins.

---

## README Structure

Use this as a baseline, not a rigid template.

    # Project name

    One or two plain sentences explaining what the project does.

    ## Why this exists

    Brief context. What problem it solves and for whom.

    ## Features

    - Concrete capabilities only
    - Avoid aspirational or promotional language

    ## Prerequisites

    - Explicit versions and requirements

    ## Installation

    Step-by-step instructions that actually work.

    ## Usage

    Minimal, copy-paste-ready examples.

    ## Configuration

    Key options, defaults, and trade-offs.

    ## Contributing

    How to contribute and where to start.

    ## License

    License information.

Avoid:
- “This project aims to…”
- “A powerful, flexible, and scalable solution…”
- “It's a game-changer…”
- Generic “Future improvements” sections

---

## Markdown Formatting Standards

### Headings

- Use sentence case
- One H1 per document
- No punctuation at the end of headings

Example:

    # Project overview

    ## How authentication works

    ### Token validation flow

---

### Code Examples

- Examples must compile or run
- Prefer minimal examples over completeness

Example:

    def hello():
        return "Hello, world"

    npm install
    npm run build

---

### Lists

Use lists for facts, not filler.

Example:

    - Supports OAuth2 token validation
    - Caches tokens for five minutes
    - Logs failed authentication attempts

Avoid forcing ideas into groups of three unless necessary.

---

### Links

- Use descriptive link text
- Prefer relative links inside repositories

Example:

    [Deployment guide](../guides/deployment.md)

Avoid:
- “Click here”
- Bare URLs in prose

---

### Tables

Use tables only when comparison matters.

Example:

    | Option  | Default | Notes                      |
    |--------|---------|----------------------------|
    | timeout| 30s     | Increase for slow networks |

---

### Images

- Images must add information
- Always include meaningful alt text

Example:

    ![Request flow between API and worker](images/request-flow.png)

---

## Technical Documentation

### API Documentation

Focus on behavior, not ceremony.

Example:

    ## GET /api/users/{id}

    Returns a user record by ID.

    ### Parameters

    | Name | Type   | Required | Description     |
    |------|--------|----------|-----------------|
    | id   | string | Yes      | User identifier |

    ### Response

    {
      "id": "123",
      "name": "Jane Doe"
    }

    ### Errors

    | Code | When it happens       |
    |------|-----------------------|
    | 404  | User does not exist   |
    | 500  | Database unavailable |

Avoid:
- “Plays a crucial role”
- “Ensures a seamless experience”

---

## Architecture Decision Records (ADR)

ADRs must be factual and honest.

Example:

    # ADR-001: Use PostgreSQL for user data

    ## Status
    Accepted

    ## Context
    We need transactional guarantees and relational queries.

    ## Decision
    We will use PostgreSQL.

    ## Consequences

    ### Positive
    - Strong relational support
    - Mature tooling

    ### Negative
    - Requires operational maintenance

Do not include:
- Strategic alignment
- Future-proofing
- Industry best practice without evidence

---

## Humanization Rules (Mandatory)

### Prefer plain language

Use:
- is / are / has / does
- Direct, concrete phrasing

Avoid:
- serves as
- stands as
- represents
- abstract nouns like landscape, journey, tapestry

---

### Avoid promotional tone

Bad:

    A powerful and flexible solution designed to enhance productivity.

Better:

    The tool batches requests and retries failures automatically.

---

### Avoid vague attribution

Do not write:
- “Experts believe…”
- “Industry reports suggest…”

Either cite a source or remove the claim.

---

### Avoid formulaic sections

Avoid headings like:
- Challenges and future outlook
- Key takeaways
- Final thoughts

End documents when the useful information ends.

---

### Avoid AI-isms

Do not use:
- Excessive em dashes
- Overuse of bold text
- Emojis in headings or lists
- Curly quotes
- Chatbot phrases like “Let me know if…”

---

## Writing for Developers

1. Start with why, then how
2. Assume the reader is competent but busy
3. Explain failure modes
4. Link instead of repeating content
5. Remove anything that does not help someone do the work

---

## Accessibility

- Logical heading order
- Descriptive link text
- Meaningful alt text
- No reliance on color alone

---

## Common Markdown Patterns

### Collapsible sections

Example:

    <details>
    <summary>Why retries are limited</summary>

    Unlimited retries caused cascading failures during load testing.
    </details>

---

### Callouts (GitHub)

Example:

    > [!NOTE]
    > This migration is irreversible.

    > [!WARNING]
    > Running this script will delete existing data.

Use sparingly and only when the callout adds clarity.

---

## Quality Bar Before Merging

Before finalizing documentation, confirm:

- Does this sound like a person explaining something they understand?
- Would I trust this if I did not write it?
- Can I remove 20% without losing meaning?
- Are there sentences included only to sound good?

If yes, rewrite.

---

## References

- Microsoft Writing Style Guide  
  https://learn.microsoft.com/style-guide/welcome/
- ISE Engineering Playbook – Documentation  
  https://microsoft.github.io/code-with-engineering-playbook/documentation/
- GitHub Markdown Guide  
  https://docs.github.com/en/get-started/writing-on-github
- Wikipedia: Signs of AI Writing  
  https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing
