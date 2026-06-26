# Multi-Agent Systems

## What is it?
Multi-agent systems use multiple AI agents working together to solve complex tasks. Each agent specializes in a role (researcher, writer, reviewer, coder, tester) and communicates with other agents to achieve a shared goal. Key aspects: orchestration (how agents coordinate), communication (message passing), task decomposition (breaking work into subtasks), and conflict resolution. Frameworks: AutoGen (Microsoft), CrewAI, LangGraph, and OpenAI Swarm.

## Why do we use it?
Complex tasks require multiple skills. One agent doing everything is inefficient and error-prone. Multi-agent systems: 1) Divide labor (each agent specializes), 2) Parallel execution (faster completion), 3) Quality through review (one agent reviews another), 4) Scalability (add more agents for bigger tasks). Companies use them for software development, content creation, research analysis, and business automation.

## Real-Time Example 1
**Software Development Team** — AutoGen with 3 agents. Step 1: Project Manager agent breaks feature request into tasks. Step 2: Coder agent writes the code. Step 3: Reviewer agent checks code quality, suggests improvements. Step 4: Tester agent writes and runs tests. Step 5: If tests fail, Coder fixes → Reviewer rechecks → Tester retests. Step 6: DevOps agent deploys when all pass. Each agent has one job, does it well. Human just reviews the final PR.

## Real-Time Example 2
**Market Research Report Generation** — CrewAI with 4 agents. Step 1: Research Lead agent decomposes "Create market analysis for electric vehicles 2024" into sub-topics. Step 2: Web Searcher agent queries multiple sources. Step 3: Data Analyst agent processes numbers and creates charts. Step 4: Writer agent synthesizes findings into a report. Step 5: Editor agent reviews for quality, consistency, and citations. Step 6: Report delivered in 2 hours instead of 2 weeks. Each agent is an expert in its role.

## Concept Example 1
**CrewAI: Simple Multi-Agent System**
```python
from crewai import Agent, Task, Crew, Process

# Define agents
researcher = Agent(
    role="Research Analyst",
    goal="Find accurate information about AI trends",
    backstory="Expert researcher with access to latest data",
    verbose=True
)

writer = Agent(
    role="Content Writer",
    goal="Create engaging blog posts from research",
    backstory="Tech writer specializing in AI topics",
    verbose=True
)

# Define tasks
research_task = Task(
    description="Research top 5 AI trends in 2024",
    agent=researcher,
    expected_output="List of 5 trends with key details"
)

writing_task = Task(
    description="Write a blog post based on research",
    agent=writer,
    expected_output="300-word blog post"
)

# Create crew
crew = Crew(
    agents=[researcher, writer],
    tasks=[research_task, writing_task],
    process=Process.sequential  # one after another
)

result = crew.kickoff()
print(result)
# Researcher finds trends -> Writer creates blog post
# No human intervention needed between steps
```
**Input:** Task description "Research and write about AI trends"
**Output:** Blog post based on research
**Explanation:** Two agents work sequentially. The researcher finds information, then the writer uses that research to create content. Each agent has a specific role, goal, and backstory that guides its behavior. CrewAI handles the task assignment and handoff automatically.

## Concept Example 2
**AutoGen: Conversational Agents**
```python
import autogen

# Configure agents
llm_config = {"config_list": [{"model": "gpt-4", "api_key": "..."}]}

# User proxy (human proxy, can execute code)
user_proxy = autogen.UserProxyAgent(
    name="User",
    human_input_mode="NEVER",  # fully autonomous
    code_execution_config={"work_dir": "coding"}
)

# Assistant agent
assistant = autogen.AssistantAgent(
    name="Assistant",
    llm_config=llm_config,
    system_message="You write Python code to solve problems."
)

# Critic agent
critic = autogen.AssistantAgent(
    name="Critic",
    llm_config=llm_config,
    system_message="You review code for correctness and style."
)

# Start conversation
user_proxy.initiate_chat(
    assistant,
    message="Create a Python function to calculate Fibonacci numbers"
)

# AutoGen automatically:
# 1. Assistant writes code
# 2. User proxy executes it
# 3. If error, assistant fixes
# 4. Critic reviews final code
# 5. Conversation continues until task is done
```
**Input:** Request to create a Fibonacci function
**Output:** Working code with review
**Explanation:** AutoGen uses a conversation-based approach. Agents talk to each other via messages. UserProxyAgent can execute code (acts as the "computer"). The Critic reviews. This creates a mini software development workflow. AutoGen handles the conversation loop, turn management, and tool execution.

## Common Mistakes
- Making agents too similar — they should have distinct roles and expertise.
- Not defining clear communication protocols — agents get confused about what to do.
- Ignoring error propagation — one agent's failure cascades to others.
- Too many agents — increases complexity, latency, and cost unnecessarily.
- No human-in-the-loop for critical decisions — agents can make wrong choices.
- Not setting timeouts — agents can loop forever.

## How Interviewers Ask This
- "What are multi-agent systems and why use them?"
- "Compare AutoGen, CrewAI, and LangGraph."
- "How do agents communicate in multi-agent systems?"
- "What is orchestration in multi-agent systems?"
- "How do you handle agent failures in a multi-agent system?"

## How To Impress The Interviewer
- Explain the different communication patterns: Sequential (chain), Hierarchical (manager delegates), Graph (DAG with dependencies), Conversational (chat between agents).
- Discuss AutoGen's two main patterns: AssistantAgent (LLM-powered) and UserProxyAgent (human proxy that executes code). They converse until task completion.
- Mention CrewAI's role-based design: each agent has role, goal, backstory. Tasks can be sequential or hierarchical. The "manager" agent delegates work.
- Talk about LangGraph for complex workflows: define state, nodes (agents), and edges (transitions). Supports conditional routing, loops, and parallel execution. More control than CrewAI.
- Know the failure modes: circular conversations (agents talking without progress), hallucination propagation (one agent's hallucination spreads), cost explosion (many API calls).

## Most Asked Questions

**Q1: What is a multi-agent system?**
A: Multiple AI agents working together on complex tasks. Each agent has a specific role. Example: A "Researcher" agent finds data, a "Writer" creates content, an "Editor" reviews — all collaborating on a report.

**Q2: Why use multiple agents instead of one?**
A: Specialization (each agent is expert in one thing), Parallelism (agents work simultaneously), Quality (review agents catch errors), Scalability (add agents for larger tasks). Example: One coding agent might write buggy code; adding a reviewer agent catches bugs before deployment.

**Q3: What is AutoGen?**
A: Microsoft's multi-agent framework. Uses conversational agents that exchange messages. Key agents: AssistantAgent (LLM), UserProxyAgent (executes code, human proxy). Example: Researcher and Coder agents discuss and iterate on a solution.

**Q4: What is CrewAI?**
A: Role-based multi-agent framework. Agents have role, goal, and backstory. Tasks are assigned to agents. Orchestration can be sequential or hierarchical. Example: A "CEO" agent leads "Researcher", "Analyst", and "Writer" agents to produce a market report.

**Q5: What is LangGraph?**
A: LangChain's library for building stateful multi-agent workflows using graphs. Nodes = agents, Edges = transitions. Supports conditional routing, loops, parallel execution. Example: An agent graph with nodes Research → Plan → Execute → Review → (if fail) back to Execute.

**Q6: How do agents communicate?**
A: Through structured messages (JSON). Message types: task assignment, result delivery, feedback, error reporting, status update. Example: Researcher sends {"type": "result", "content": "Data found", "source": "web"} to Writer.

**Q7: What is orchestration in multi-agent systems?**
A: The mechanism controlling which agent does what and when. Types: Sequential (A → B → C), Hierarchical (Manager delegates to workers), Conversational (agents talk freely), Graph-based (defined DAG). Example: CrewAI's process=Process.sequential runs tasks one after another.

**Q8: How do you handle failures in multi-agent systems?**
A: 1) Retry (agent retries with different approach), 2) Escalate (another agent handles), 3) Revert (undo and restart subtask), 4) Human handoff (if all agents fail). Example: Coder agent's code fails tests → sends to Reviewer → Reviewer identifies issue → Coder fixes → Tester retries.

**Q9: What are the challenges with multi-agent systems?**
A: Cost (multiple API calls), Latency (sequential agent processing), Coordination complexity, Debugging difficulty (which agent caused the issue?), Hallucination propagation (one wrong fact spreads). Example: A 5-agent system might cost 5x more per task than a single agent.

**Q10: When should you not use multi-agent systems?**
A: Simple tasks (single agent suffices), Cost-sensitive applications (multi-agent is expensive), Low-latency requirements (sequential agents are slow), Well-defined fixed workflows (use chains instead). Example: A simple Q&A bot doesn't need 3 agents — one is enough.

**Q11: What is the difference between AutoGen and CrewAI?**
A: AutoGen uses conversational agents (free-form chat). CrewAI uses role-based structured agents (defined roles, tasks, processes). CrewAI is simpler for fixed workflows. AutoGen is more flexible for complex conversations. Example: CrewAI for "research → write → edit" pipeline. AutoGen for "discuss and iterate on a solution."

**Q12: How do you test multi-agent systems?**
A: 1) Unit test each agent individually, 2) Integration test agent pairs, 3) End-to-end test full workflow, 4) Monitor metrics (task completion rate, cost per task, agent failure rate, human intervention rate). Example: Test Researcher agent alone, then Researcher+Writer pair, then full crew with test prompts.
