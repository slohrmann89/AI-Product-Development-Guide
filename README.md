# AI Product Development Guide

# V0.2

Added Design System in AI and in Code.

# Intro

AI can now augment your entire product team. You’re becoming the moderator of the process, the judge and the jury. While strategic thinking, self-starting, taste, style, and judgement are becoming more important, craft still remains very much essential. There are many times when you need to intervene. Otherwise the quality of the output drops significantly. Often it goes in a completely unwanted direction. That’s where craft still matters. And will continue to matter. AI is a tool. It *augments*, it does not replace.

AI makes it possible to start at any point in this journey. So this is definitely not a linear workflow. You can start anywhere and continue to add pieces of the puzzle afterwards. With the incredible speed you gain with AI it basically doesn’t matter anymore. Still: building the right thing requires some discipline. At some point you need to finish the puzzle, or you are in danger of producing waste. At lightning speed. But still waste.

AI is incredibly good at two things: replicating and transforming. Therefore our goal is to basically give the AI the finished result in as much detail as possible so that it only has to replicate and transcode it.

This is but one approach. Take it, adapt it, have fun with it.

# Business Idea

Let’s begin at the beginning. Start with a problem to solve. That’s the basis for each product, each feature. A need to fulfill. A pain point to ease.  A problem to solve. This needs empathy. No AI can help you here. This should be a no-brainer: Validate the hypothesis with real humans. https://www.youtube.com/watch?v=Th8JoIan4dg

```
Please formulate the product idea for solving this problem:
-> insert problem statement <-
- Write brief Vision and Mission paragraphs
- Formulate a strategy based on the playing to win framework
- List of possible features + very short description of each
- Ethical concerns

Be concise. 1500 characters max.
```

Employ these skills:

- **Problem framing / Business Analyst** (BMAD – `bmad-bmm-analyst`, `/product-brief`, `/brainstorm-project`, `/research`) — [aj-geddes/claude-code-bmad-skills](https://aj-geddes.github.io/claude-code-bmad-skills/)
- **Discovery interview script** — [`slgoodrich/agents`](https://github.com/slgoodrich/agents)

### Vetting the idea

Now that we have a business idea, we vet it thoroughly. Use the research mode in Perplexity to Investigate your idea with this prompt:

```
This is the business idea:
-> input idea here <-

Please research:
- Competition (make this a table)
- Reach (TAM, SAM, SOM…)
- Possible MRR for a realistic reach
- Generate a SWOT matrix
- For the last paragraph: Play devils advocate: beat up the idea

Keep it short and sweet, under 1500 characters.
```

If there’s competition that’s great! It means there’s value. Now research your competition. And then research what their customers dislike about their product. That’s where you can improve upon.

You can also make use of these skills:

- **Competitive landscape** — [MCPMarket: Market Research & Competitive Analysis](https://mcpmarket.com/tools/skills/market-research-competitive-analysis-1)
- **Market sizing (TAM/SAM/SOM)** — [deanpeters/Product-Manager-Skills](https://github.com/deanpeters/Product-Manager-Skills)

### Positioning

In a world where it only takes 5 minutes to build an app from scratch, you can’t outperform your competition. The only moat / defensible value that you can build is your brand. This positioning skill helps you define one of the cornerstones of your brand: your unique position, your unique offering. Use this skill to find your unique positioning:

[b2b-positioning.skill](AI-Product-Development-Guide/b2b-positioning.skill)

- **Devil's advocate / Pre-Mortem** — [deanpeters/product-manager-prompts](https://github.com/deanpeters/product-manager-prompts)
- **Ethics & misuse scan** — [skysheng7/ai-agent-prompts](https://github.com/skysheng7/ai-agent-prompts)

# Product Concept & Strategy

You should now have a report telling you if your idea is worth pursuing. Additionally you can test it with this handy AI evaluator: https://syra.up.railway.app/. If there’s competition it means there’s a need. Steal from your competition. Let AI research what their users do not like about their product and there you have a list of features you can improve on.

Put on your PM hat and prioritize the features. Impact / Effort, WSJF, RICE, MoSCoW, NSFW, bobs your uncle. Or use this skill to prioritize for you: https://github.com/deanpeters/Product-Manager-Skills

Then generate a PRD for the first feature: Use https://www.chatprd.ai/ to create the PRD each feature.

This will be a very detailed instruction manual or plan of what we want to build.

Alternative if you don’t have ChatPRD: Use the [create-prd.md](http://create-prd.md) from https://github.com/snarktank/ai-dev-tasks to generate the PRD:

```
Here's the feature I want to build: [Describe your feature in detail]
Reference these files to help you: [Optional: @file1.py @file2.ts]
```

Read and redact the PRD. This is a step where you can steer the development. Delete anything that’s unnecessary.

# Feature Idea

Once we have defined our product and strategy, we can start experimenting with features.

### Opportunity Solution Tree

We shift the frame: turn the problem into an opportunity. Based on the problem we build solution ideas. Use an AI to generate solution ideas. ChatPRD is great at generating feature ideas! Formulate hypotheses for the solutions you want to test.

### Working Hypothesis & Data driven

Formulate hypotheses about the solution for the problem that you’re solving.

A good product hypothesis is **specific, measurable, falsifiable**, and ties a change to an outcome for a target audience.

**Template:**

```
For [user segment] with [problem],
if we change [X] (feature/UX/pricing),
we expect [Y] (metric movement by [amount] in [time])
because [reason]. We’ll test with [method Z] on [sample],
success = [threshold].
```

### User Research

For me this falls under the category of “remains relatively unchanged”. The ability to understand a pain point, a need, a challenge or a problem that your audience has is still uniquely human. 

Dovetail is amazing at speeding up the analysis of recorded interviews. Transcribing, summarising - this works well. And it’s great, because it saves a lot of time. Everything that comes afterwards like automated tagging… didn’t see great results there.

The automated analysis of hundreds of feedback items over various sources to discover trends is amazing. https://docs.dovetail.com/help/channels

Yes, there are other tools which offer AI automated user interviews.

Yes, there even more tools which offer automated usability tests.

I would recommend using this with caution. This is something where hallucinations can happen quickly.

And the risks that come with incorrectly interpreted user research results are absolute.

# Product Design

### User Flow

Generate a User Flow based on the PRD with https://uxpilot.ai/ux-frameworks/user-flows-workshop

We basically want to know which screens we’ll have to design in the end. You can also ask your AI of choice to give you a list of screens + description for the screens.

## Design System

Simplicity is the ultimate sophistication. Consistent simplicity is the pro max version of it.

For integrating AI and Design Systems there are multiple use cases in my mind:

- Starting from scratch with a rough design. This is my usual workflow: I design something very rough first, which I later have to clean up. I want the AI to do the cleaning for me. To create clean structured components and layouts from the messy chaos that I left in the wake of creating a design.
- Same issue when you have a rough first design from Claude or Stitch and want to create overall in your screens consistency
- Starting with a complete design system and building up UIs from this
- Scaling your product with consistent designs

So let’s create an entire design system from scratch with AI. After all that’s what AI is great at: recreating something that has been done before. And Design Systems have been done a thousand times.

### Design System in AI

First we gather inspiration in order to Create an Art Direction, a Style. Shapes, Colors, Typescale, Rythm,… go hunt for inspiration here

- https://mobbin.com/
- https://godly.website/
- https://land-book.com/
- https://www.designspiration.com/
- https://dribbble.com/

Just like the [AGENTS.md](http://AGENTS.md) file for your AI coding you need and [DESIGN.md](https://stitch.withgoogle.com/docs/design-md/overview) file for your AI designing your UI. While most tools claim to create a “Design System” all they really do create is a rough style. Design tokens, nothing more. Only a few tools go further.

- Google Stitch only generates design tokens, no real design system
- https://github.com/dominikmartn/hue creates a whole design system including components
- [Claude Design](https://www.anthropic.com/news/claude-design-anthropic-labs) creates components, tone of voice and even logo if you don’t have one

You can alternatively grab a DESIGN.md from this collection: https://getdesign.md/

Or from this website: https://neuform.ai/

But these are rather just the design tokens.

### Design System in Figma

Let’s bring our style to Figma. This is where our design system needs to be used. This is where we craft designs. And we still need to craft designs.

We will use https://www.figma.com/community/file/1203061493325953101 shadcn/ui - Design System ****as base. We’ll use the [Figma MCP](https://github.com/figma/mcp-server-guide) in order to let the AI update the design system with our desired style.

```
Please adapt all primitives, components, styles, and colors in this file to match this shadcn theme.
```

*You now have a design system ready to go with your desired style and can ask the AI of your choice to design screens with it.*

#### **Design System from Scratch**

If you do not want to use Shadcn, but rather create your own design system in figma [Ubers](https://www.uber.com/ca/en/blog/automate-design-specs/) [uSpec](https://docs.uspec.design/) comes in very handy. It creates design documentation in figma based on your implementation.

### Design System in Code

The goal is to have a system that always stays in sync - figma and code. This gives us the ultimate speed and control. The system consists of two parts: Design Tokens (=Buttons,…) and UI Components (=Colors, Fonts,…).

**Design Tokens**

- Naming tokens is often the hardest part. Use this tool to make it easier on yourself: https://www.namedesigntokens.guide/builder
- Export the tokens from Figma to JSON using Figma Variables Export. Open Variables Window in Figma, Right Click on a Collection and “Export mode”.
- Load the JSON into Style Dictionary https://styledictionary.com/ and it transforms your tokens into CSS variables or Tailwind config.

**Components**

- Ask the AI to create a design system using your newly created tokens, your DESIGN.md in the in [Storybook](https://storybook.js.org/) or any other design system framework you like.
- Use any component library / design system of your choosing as base. I’d recommend [shadcn](https://www.figma.com/community/file/1203061493325953101), since AI is great at using this to code as well.
- The UI Components come from the other side: from code. Ask the AI to implement the them according to the implementation, using the tokens in your system.
- To keep everything in sync set up a GitHub Action that listens to your Token changes. A push in Figma results in a Pull Request in the repo.

### UI Design

Using the Flow and our design system we can [use AI to design the feature for us](https://www.figma.com/blog/the-figma-canvas-is-now-open-to-agents/).

I recently experimented with having Claude Code design Screens in Figma via the https://github.com/southleft/figma-console-mcp This produced the most consistent designs so far. 30 screens in 30 minutes. Since then Figma released their own MCP https://github.com/figma/mcp-server-guide which even knows autolayout and produces even better results.

Alternatively Design the Screens with AI using one of those tools:

- https://www.lunagraph.com/
- https://efecto.app/
- https://paper.design/
- https://www.pencil.dev/
- https://stitch.withgoogle.com/
- https://www.relume.io/
- https://uxpilot.ai/de
- https://bolt.new/
- https://lovable.dev/
- https://replit.com/
- https://v0.app/
- https://www.aura.build/

### UI Designs from Code

Figma MCP can create UI Designs for the code that you have already generated. This helps if you want to adjust the design of an existing feature.

```
Create all screens in this code in Figma.
```

### Spec

Based on everything we have gathered so far – the User Flow, the UI Design and the PRD – we create the **Spec**.

The Spec is the source of truth for each feature.

If you can, do write tests for your business logic and put them in the spec, so you can later watch as they turn green, while the AI codes. 

This is where you can steer development. Put maximum effort into writing a good spec.

[Spec Example](AI-Product-Development-Guide/Spec-Example.md)

# Product Development

### Scaffolding

AI is not great at scaffolding. If you have a really complex technical setup with multiple frameworks involved, it's better to start from a clean template. This helps to get the initial packages setup correctly. Let the AI take it from there.

### Package Discovery

Feed the refined spec into your AI of choice with "Heavy Thinking" to search for and recommend specific, well-maintained GitHub packages (e.g., for a WYSIWYG editor) to avoid building complex components from scratch.

Search for unique UI Components here: https://uiverse.io/elements

### Guardrails

By now AI Coding setups have become whole systems. Like this one for example https://github.com/affaan-m/everything-claude-code or this one https://github.com/pridiuksson/cursor-agents or this one https://github.com/ChrisWiles/claude-code-showcase

The least you can do though is give the Agents some Guardrails with:

[AGENTS.md](AI-Product-Development-Guide/AGENTS.md)

[architecture_setup.md](AI-Product-Development-Guide/architecture_setup.md)

and a *lessons.md* 

## AI Coding

It’s time to code! With the previously generated context

- A User Flow
- A UI Design
- A Spec

we can take all of this and write the Prompt for the feature.

```
Implement this feature according to the specifications in @spec
Stick to the attached design meticulously.
This is the frontend code:
This is the user flow:
```

Provide the Design via Figma MCP or as a screenshot, doesn’t really make a difference tbh.

Provide the UI Code → Get the code from figma in dev mode. Yes, the MCP does this, the code from the plugins inside Figma is better.

Make it pretty using this skill: https://github.com/Leonxlnx/taste-skill

*Press enter. You should now see code.*

### Cloud Agents

I have tried Cursors Cloud agents with the integration in Linear. This setup works surprisingly well. Added benefit: you can integrate it in slack as well and steer the entire development workflow from your smartphone on a beach while slurping a tequila sunrise.

### Getting found better

https://github.com/AgriciDaniel/claude-seo

https://github.com/nowork-studio/toprank/tree/main

### Chunking

Let the AI work in small steps. That gets you the best results, as it simply gives you more tokens per feature.

Either use Cursors Plan mode to generate a detailed, step-by-step implementation plan

or use the [`generate-tasks.md`](http://generate-tasks.md) from https://github.com/snarktank/ai-dev-tasks

# Review & Testing

**Review everything that is generated.**

Or use AI to do this for you:

https://www.sonarsource.com/products/sonarqube/

https://www.coderabbit.ai/

https://www.qodo.ai/

https://snyk.io/de/product/snyk-code/

But seriously: do review what the AI is doing. Otherwise you end up releasing all of your passwords, user data and worse into the internets. After all you remain accountable. And for all the times where you’d better not be accountable … just use this: https://github.com/jayphelps/git-blame-someone-else

### Security

AI can release your entire app and all its data to the world. Anthropic showed us how this can happen. Make sure you at least check the important parts of your app.

Use this to get an overview of what is being developed:

- https://github.com/Lum1104/Understand-Anything

and then use these skills to check the important parts (everything which is handling money and or your customers data) for security issues.

- **Security Review** — [anthropics/claude-code-security-review](https://github.com/anthropics/claude-code-security-review)
- https://github.com/cosai-oasis/project-codeguard/tree/main

### Accessibility

The thing where probably there never was time left to do it. With all the time freed up now we can focus on this very important thing: A11y.

- **UX Checklist (WCAG, States, A11y)** — [playbooks: ui-design-review](https://playbooks.com/skills/rknall/claude-skills/ui-design-review)

# Feedback loop & Self learning system

Create a `tasks/lessons.md` file. For everything the AI struggled with have it write a learning to this file. For example it still sucks at rendering SVG icons in React Native. I have a learning entry for how to do this in my lessons.md.

Further we have this in the AGENTS.md, which updates the file after any learning.

```
After ANY correction from the user: update tasks/lessons.md with the pattern
```

[Lessons.skill](AI-Product-Development-Guide/Lessons-skill.md)

Claude Code does this automatically.

If you have a KPI you want to consistently improve, try: https://github.com/karpathy/autoresearch

---

# Skill sources

Make your system even more powerful. Give it skills. Mad Skills.

1. [**mcpmarket.com/tools/skills**](https://mcpmarket.com/tools/skills) – grösste Skill-Sammlung für Claude Code
2. [**playbooks.com/skills**](https://playbooks.com/skills/delorenj) – kuratierte Claude-Skills
3. [**skills.sh**](https://skills.sh/affaan-m/everything-claude-code/security-review) – Community-Skill-Registry
4. [**claude-plugins.dev/skills**](https://claude-plugins.dev/skills/@rknall/claude-skills/ui-design-review) – Weitere SKILL.md-Indexierung
5. https://tessl.io/registry/discover
6. https://www.figma.com/community/skills

---

# Sources

*We draw inspiration from these guides. Thank you.*

- https://playbook.fullstackbuilder.ai/
- https://www.shapeof.ai/
- https://www.youtube.com/watch?v=eh8bcBIAAFo
- https://medium.com/design-bootcamp/7-ways-claude-becomes-your-design-superpower-635a96c82f22
- https://github.com/snarktank/ai-dev-tasks
- https://hvpandya.com/llm-design-systems
- https://youtu.be/1aI7pAlkz4w?is=CwBGa0KuHonoW6TP
- https://www.uber.com/en-CA/blog/automate-design-specs/
- https://alaniswright.com/blog/using-cursor-and-mcp-as-a-product-manager/
- https://github.com/obra/superpowers
- https://www.builder.io/blog/design-system-ai-automation
- https://muz.li/blog/figma-just-opened-the-canvas-to-ai-agents-heres-what-it-means-for-designers/?utm_source=extension&utm_medium=click&utm_campaign=muzli
- https://www.linkedin.com/pulse/creating-design-system-style-dictionary-amitabha-ghosh-npmqc/
- https://medium.com/typeforms-engineering-blog/supercharging-design-system-docs-with-mcps-cba4a8a96960
- https://abduzeedo.com/hue-claude-code-design-system