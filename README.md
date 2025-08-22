# PerlAI: A Modern, Robust AI Framework

## Overview

PerlAI is an AI framework designed to leverage Perl's unparalleled strength in text processing, data manipulation, and its robust, mature ecosystem (CPAN). This project aims to create a modular, scalable, and reliable system for building sophisticated AI-powered applications.

The core philosophy is to use Perl as an intelligent "router" or orchestrator, directing user prompts and data through a series of specialized, purpose-built agents. This agent-based architecture allows for complex workflows, self-correction, and continuous improvement.

-----

## Core Principles

  * **Perl-First:** Utilize the speed and expressiveness of modern Perl for data-heavy and text-centric AI tasks.
  * **Modular & Agent-Based:** The system is built from independent components (agents) that can be developed, tested, and improved in isolation.
  * **Robust & Reliable:** Leverage Perl's long history of stability and extensive testing culture to build a production-ready framework.
  * **Interoperable:** Easily connect to modern AI models, databases, APIs, and data sources through the vast CPAN library.

-----

## Architecture

The framework is composed of several key components that work in concert:

1.  **API Interface:**

      * A web server (e.g., using `Mojolicious`) that exposes an OpenAI-compatible API endpoint.
      * This ensures immediate compatibility with a wide range of existing UI clients, starting with **ChatBox.ai**.

2.  **Core Router:**

      * The brain of the system. It receives incoming requests from the API.
      * Uses an initial LLM call to analyze the user's intent and determines the appropriate agent or sequence of agents to handle the request.

3.  **Agent System:**

      * A collection of specialized Perl modules, each designed for a specific task.
      * **Initial Agents Planned:**
          * **QueryAnalyzer:** The first agent that assists the Core Router in breaking down the user's request.
          * **WebSearch:** Fetches information from the internet.
          * **ContentScraper:** Strips HTML, ads, and other non-relevant data from web content, providing clean text.
          * **RAGIngestor:** Processes the clean text and ingests it into a vector database for Retrieval-Augmented Generation.
          * **ResponseEvaluator:** An agent that reviews a generated response for accuracy, tone, and quality before sending it to the user.
          * **Summarizer:** Provides summaries of large datasets or text.

4.  **CORE AI Model Interface:**

      * A centralized module for communicating with various third-party AI models (e.g., OpenAI, Cohere, local models). This abstracts the specific API details from the rest of the framework.

5.  **Database Backend:**

      * Stores conversation history, user settings, agent metadata, and logs.
      * Will start with **SQLite** for simplicity and evolve to support **PostgreSQL/MySQL** for scalability.

6.  **Output Generation:**

      * A dedicated module for creating various output formats based on agent results.
      * Capabilities will include generating text files (`.txt`), documents (`.docx`), spreadsheets (`.xlsx`), and images (`.png`, `.svg`).

7.  **Data Connectors:**

      * Interfaces for fetching data from external sources, such as databases, APIs, and specialized endpoints like **MCP gateways**.

-----

## Proof of Concept (POC) Roadmap

This project will be built iteratively through a series of defined milestones.

### Milestone 1: The Foundation (POC v0.1)

  * **Goal:** Establish the basic plumbing and a single end-to-end workflow.
  * **Tasks:**
      * Set up the project structure with `Dist::Zilla` or similar.
      * Build the core **API Interface** using `Mojolicious` to accept a prompt.
      * Implement the **CORE AI Model Interface** to connect to a single LLM provider (e.g., OpenAI).
      * Create a simple "EchoAgent" that passes the prompt to the LLM and returns the response.
      * Integrate **SQLite** for basic logging of interactions.
  * **Outcome:** A user can send a prompt via ChatBox.ai and get a direct response from an LLM, routed through the Perl framework.

### Milestone 2: The Intelligent Router (POC v0.2)

  * **Goal:** Implement the core routing logic.
  * **Tasks:**
      * Develop the **Core Router** to perform an initial LLM call to classify the user's intent.
      * Create a second agent, e.g., a `CalculatorAgent` for simple math.
      * The router should now decide whether to send a prompt to the `GeneralAgent` (from M1) or the `CalculatorAgent`.
  * **Outcome:** The framework can now route tasks to different agents based on intent.

### Milestone 3: RAG Implementation (POC v0.3)

  * **Goal:** Build a basic Retrieval-Augmented Generation pipeline.
  * **Tasks:**
      * Create the **WebSearch** agent.
      * Create the **ContentScraper** agent to clean the search results.
      * Integrate a vector store (e.g., using `AI::VectorStore`) and build the **RAGIngestor** agent.
      * Modify the router to use this RAG pipeline for relevant queries.
  * **Outcome:** The framework can answer questions by searching the web, processing the content, and synthesizing an answer from it.

### Milestone 4: Response Quality and Output (POC v0.4)

  * **Goal:** Introduce self-correction and multi-format output.
  * **Tasks:**
      * Build the **ResponseEvaluator** agent. The router will pass generated responses through this agent for a quality check before finalizing the answer.
      * Implement the **Output Generation** module to create a `.txt` file as an output option.
  * **Outcome:** The system can now evaluate its own answers and can generate simple file-based outputs.
