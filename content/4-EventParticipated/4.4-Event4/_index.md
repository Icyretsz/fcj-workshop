---
title: "Generative AI with Amazon Bedrock"
date: "`r Sys.Date()`"
weight: 4
chapter: false
pre: " <b> 4.4. </b> "
---

# Summary Report

### Event Vision

- To provide a strategic guide to Generative AI, from the foundational principles of large language models to the sophisticated art of prompt engineering.
- To unlock the power of Retrieval Augmented Generation \(RAG\) for building AI that is grounded in proprietary, real-time data.
- To introduce the next evolutionary step in AI: autonomous, goal-oriented agents that can reason and act.
- To unveil **Amazon Bedrock AgentCore**, a comprehensive platform designed to bridge the chasm between AI prototypes and secure, scalable production systems.

### Speakers

- **Lam Tuan Kiet** – Sr. DevOps Engineer, FPT Software
- **Danh Hoang Hieu Nghi** – AI Engineer, Renova Cloud
- **Dinh Le Hoang Anh** – Cloud Engineer Trainee, First Cloud AI Journey

### Key Highlights

#### A Glimpse into the Future

*"The journey of AI is an evolution in autonomy."* This central theme framed the entire workshop, charting a course from simple AI assistants that follow rules to fully autonomous Agentic AI systems that can reason, plan, and execute complex workflows with minimal human oversight.

#### Part 1: Mastering the Fundamentals with Amazon Bedrock

- **The Foundation Model Revolution:** The session began by contrasting traditional ML models with the broad, general-purpose **Foundation Models** \(FMs\). Amazon Bedrock was positioned as the secure gateway to a diverse range of cutting-edge FMs.
- **The Art of the Prompt:** Prompt Engineering was deconstructed as the skill of communicating effectively with an AI. The session covered a spectrum of techniques from **Zero-Shot** and **Few-Shot** to advanced **Chain-of-Thought** prompting.
- **Retrieval Augmented Generation \(RAG\): Grounding AI in Reality:** RAG was presented as the critical technology for enterprise AI. By using **embedding models** like Amazon Titan to convert private data into vector representations, RAG allows the AI to retrieve relevant, up-to-date information *before* generating an answer, combating hallucinations and tailoring responses to specific business contexts.

#### Part 2: The Evolution into Autonomous Agents

- **Bridging the Prototype-to-Production Chasm:** The speakers identified the critical hurdles that prevent promising AI prototypes from becoming valuable business tools: **Performance, Scalability, Security, and Governance.**
- **The Rise of Agentic AI:** The workshop then pivoted to the future: AI Agents. These are not just chatbots; they are systems designed to achieve specific goals by automating entire workflows, leveraging open-source frameworks like **LangChain, LlamaIndex, and Crew.AI**.

#### Part 3: Introducing Amazon Bedrock AgentCore

- **The Production-Ready Platform for Agents:** The highlight of the event was the introduction of **Amazon Bedrock AgentCore**, a comprehensive platform designed to solve the production chasm. AgentCore provides all the foundational services needed to run agents securely and at scale:
    - **Runtime & Identity:** Securely execute and manage agent operations.
    - **Memory:** Provide agents with context for coherent interactions.
    - **Tools:** Equip agents with a **Browser Tool** to access live web data and a **Code Interpreter** to perform calculations.
    - **Gateway:** A secure entry point for agents to interact with company APIs.
    - **Observability:** Gain deep operational insights into agent performance, cost, and behavior.

### From Theory to Practice: The Live Demos and Code Blueprints

The workshop transcended theory by grounding every concept in practical, hands-on code, referencing two key GitHub repositories as invaluable resources for attendees.

#### The Foundational Cookbook: `aws-samples/amazon-bedrock-samples`

This official AWS repository was presented as the essential \\"cookbook\\" for mastering the core components of Bedrock. The demos showcased how this collection of Jupyter notebooks and code samples provides a flight simulator for AI development, allowing engineers to:

- **Experiment with Prompting:** Explore dozens of examples for Zero-shot, Few-shot, and Chain-of-Thought prompting across different models like Claude and Llama.
- **Build a RAG Pipeline from Scratch:** Step-by-step guides demonstrated how to use Amazon Titan to create embeddings, store them in a vector database, and build a complete, functional RAG pipeline for Q&A over private documents.
- **Master the APIs:** Provide clear, reusable code snippets for interacting with virtually every feature of the Bedrock service, from text generation to image creation.

#### The Capstone Project: `ihatesea69/Bedrock_AgentCore`

This repository served as the capstone demo, illustrating how to assemble the individual components from the \\"cookbook\\" into a sophisticated, functioning AI agent using the new AgentCore platform. The live demonstration walked through this repository to showcase how to:

- **Define an Agent:** Structure the agent's identity, instructions, and goals in code.
- **Equip the Agent with Tools:** Grant the agent the ability to perform actions beyond text generation, such as calling external APIs or interpreting code.
- **Orchestrate a Mission:** Tie everything together in a live example where the agent receives a complex request, autonomously chooses the right tools, retrieves necessary information, and executes a multi-step plan to achieve its goal. This made the abstract concept of \\"Agentic AI\\" tangible and achievable.

### Key Takeaways

#### Your Quick-Start Blueprint: Applying This to Your Work

1. **Build a RAG-Powered Expert:** Clone the `amazon-bedrock-samples` repository and adapt the RAG notebooks to connect to your own internal documentation, creating a powerful, context-aware internal search engine.
2. **Prototype Your First Agent:** Use the `Bedrock_AgentCore` repository as a template. Define a simple agent that automates a multi-step business process unique to your team, such as generating a daily sales report by calling an internal API and summarizing the results.
3. **Refine Prompts with the Cookbook:** Take an existing AI workflow and use the diverse examples in the `amazon-bedrock-samples` repo to upgrade your prompts from simple instructions to sophisticated, few-shot examples, dramatically improving output quality.
4. **Explore AgentCore Tools:** Investigate how the tools within Bedrock AgentCore, like the Browser Tool or Code Interpreter, could solve a specific business problem that requires live data or dynamic calculations.

### Event Experience

This workshop was a fascinating journey through the entire landscape of modern AI. The inclusion of comprehensive GitHub repositories transformed it from a theoretical lecture into an actionable masterclass. Attendees left not just with knowledge, but with the specific code blueprints needed to begin building the next generation of AI-driven applications immediately. The introduction of Amazon Bedrock AgentCore, backed by a practical demo, provided a clear and compelling roadmap for moving beyond simple AI prototypes to create secure, scalable, and truly autonomous enterprise agents.

#### Some event photos

{{< image-grid >}}
<img src="/images/event4.1.jpeg" alt="Event Photo 1">
<img src="/images/event4.2.jpeg" alt="Event Photo 2">
<img src="/images/event4.3.jpeg" alt="Event Photo 3">
<img src="/images/event4.4.jpeg" alt="Event Photo 4">
{{< /image-grid >}}
