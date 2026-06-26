# Agents

## What is it?
An LLM agent is an AI system that can use tools, reason, plan, and take actions autonomously to achieve goals. Unlike a simple chatbot (text in → text out), agents can: call APIs (tool calling), search the web, run code, query databases, and remember past interactions (memory). The ReAct pattern (Reasoning + Acting) is the most popular framework: Thought → Action → Observation → Thought...

## Why do we use it?
Agents extend LLMs beyond text generation into real-world action. They automate complex multi-step tasks: booking flights (search flights → check prices → book → send confirmation), data analysis (query DB → run Python → plot chart → summarize), software development (write code → run tests → fix bugs → deploy).

## Real-Time Example 1
**Travel Booking Agent** — Step 1: User says "Book a flight to London next Tuesday under $500." Step 2: Agent reasons "Need to check flights, hotels, and user preferences." Step 3: Calls flight search API (tool). Step 4: Gets results, reasons "Found 3 options under $500." Step 5: Presents options. Step 6: User selects a flight. Step 7: Agent calls booking API. Step 8: Confirms booking and adds to calendar. All without writing explicit code for each step.

## Real-Time Example 2
**Automated Data Analyst** — Step 1: User uploads a CSV and asks "What were our top 3 products by revenue in Q4?" Step 2: Agent reads the CSV (file tool). Step 3: Reasons "Need to filter Q4 and group by product." Step 4: Writes PostgreSQL query (SQL tool). Step 5: Gets results, reasons "Need to visualize." Step 6: Generates matplotlib bar chart (Python tool). Step 7: Sends chart and summary to user. Step 8: User asks "Why did product X decline?" Agent continues analysis loop.

## Concept Example 1
**Simple ReAct Agent**
```python
from openai import OpenAI
import json

client = OpenAI()

def calculate(expression):
    return eval(expression)

tools = [
    {
        "type": "function",
        "function": {
            "name": "calculate",
            "description": "Evaluate a math expression",
            "parameters": {
                "type": "object",
                "properties": { "expression": {"type": "string"} },
                "required": ["expression"]
            }
        }
    }
]

messages = [{"role": "user", "content": "What is (15 * 7) + 23?"}]
response = client.chat.completions.create(
    model="gpt-4",
    messages=messages,
    tools=tools,
    tool_choice="auto"
)

# Check if model wants to call a tool
if response.choices[0].message.tool_calls:
    tool_call = response.choices[0].message.tool_calls[0]
    args = json.loads(tool_call.function.arguments)
    result = calculate(args["expression"])

    messages.append(response.choices[0].message)
    messages.append({
        "role": "tool",
        "tool_call_id": tool_call.id,
        "content": str(result)
    })

    # Get final answer
    final = client.chat.completions.create(model="gpt-4", messages=messages)
    print(final.choices[0].message.content)  # "(15 * 7) + 23 = 128"
```
**Input:** User asks a math question that needs calculation
**Output:** Model recognizes it needs the calculator tool, calls it, gets result, and answers
**Explanation:** The model doesn't do math internally — it detects a function call opportunity, calls `calculate(expression)`, gets the result, and formats the answer. This is the core of agentic behavior: the LLM orchestrates external tools rather than relying on its own computation.

## Concept Example 2
**ReAct Pattern (Thought-Action-Observation Loop)**
```python
import requests

def search_web(query):
    # Simplified search (would use real API in production)
    return f"Results for: {query}"

def react_agent(task, max_steps=5):
    messages = [{"role": "user", "content": task}]
    
    for step in range(max_steps):
        response = client.chat.completions.create(
            model="gpt-4",
            messages=messages
        )
        output = response.choices[0].message.content
        print(f"Step {step}: {output}")
        
        # Agent naturally follows ReAct in its response
        messages.append({"role": "assistant", "content": output})
        
        # Check if task is done (has final answer)
        if "Answer:" in output:
            return output
        
        # If agent needs to search, add observation
        if "Search:" in output:
            query = output.split("Search:")[1].split("\n")[0].strip()
            observation = search_web(query)
            messages.append({"role": "user", "content": f"Observation: {observation}"})
    
    return "Max steps reached"

result = react_agent("What is the population of Japan?")
print(result)
# Step 1: Thought: I need to find the population of Japan.
# Action: Search: "Japan population 2024"
# Step 2: Observation: Japan population is 124 million
# Answer: The population of Japan is approximately 124 million.
```
**Input:** Task requiring external knowledge
**Output:** Agent searches, observes, and answers
**Explanation:** The ReAct pattern alternates between reasoning (Thought), taking action (Search), and incorporating observations. Each cycle builds on the previous. This is superior to asking the LLM directly because it can access current information.

## Common Mistakes
- Not giving agents clear constraints (budgets, time limits, safety rules).
- Allowing agents to call destructive tools without confirmation (delete, write).
- No human-in-the-loop for important decisions — agents should ask for confirmation.
- Infinite loops — always set a max step limit.
- Poor tool descriptions — the LLM can't use tools it doesn't understand.
- Not handling errors — tools fail, APIs timeout, agents need error recovery.

## How Interviewers Ask This
- "What is an LLM agent?"
- "Explain the ReAct pattern."
- "How does tool calling work?"
- "What is the difference between an agent and a chain?"
- "How do you handle errors in agent systems?"

## How To Impress The Interviewer
- Explain that agents use the ReAct loop: Thought (reason about current state) → Action (choose a tool) → Observation (tool result) → repeat until done.
- Discuss function calling in OpenAI: the model outputs structured JSON (function name + arguments), the application executes the function and returns results.
- Talk about memory types: short-term (conversation history), long-term (vector DB), episodic (past task experiences).
- Mention agent limitations: they can hallucinate tool calls, get stuck in loops, and take too many steps.
- Know about multi-agent systems: multiple specialized agents (planner, executor, reviewer) collaborating on complex tasks.

## Most Asked Questions

**Q1: What is an LLM agent?**
A: An AI system that uses an LLM as a "brain" to reason, plan, and use tools to accomplish tasks autonomously. Example: Booking agent that searches flights, compares prices, and books tickets without step-by-step instructions.

**Q2: What is the ReAct pattern?**
A: Reasoning + Acting loop. Thought → Action → Observation → Thought... Example: "Thought: I need the weather. Action: call_weather_api. Observation: 25°C. Thought: Now I can answer. Final: It's 25°C."

**Q3: What is tool calling (function calling)?**
A: The LLM outputs structured JSON to call predefined functions. The application executes the function and returns results. Example: LLM outputs {"name": "search_db", "arguments": "{\"query\": \"SELECT * FROM products\"}"}.

**Q4: What types of memory do agents have?**
A: Short-term (conversation within context), Long-term (vector DB with past conversations), Working memory (current task state). Example: Agent remembers user preferences from previous conversations via vector DB retrieval.

**Q5: What is planning in agents?**
A: Breaking a complex task into subtasks before execution. Example: "Plan: 1) Search for flights 2) Find hotels 3) Compare prices 4) Book 5) Confirm." The agent creates and follows this plan.

**Q6: How do you prevent agents from getting stuck in loops?**
A: 1) Max step limit (e.g., 10 iterations), 2) Diverse tool results (vary observations), 3) Timeout per step, 4) Loop detection (if same action repeated 3+ times, break). Example: After 5 steps without answer, agent defaults to asking user.

**Q7: What is the difference between a chain and an agent?**
A: Chains follow predefined sequences (fixed). Agents make dynamic decisions (flexible). Example: A chain always does "search → summarize → answer". An agent might search → search again → calculate → answer based on what it finds.

**Q8: How do you handle tool errors in agents?**
A: Return error description as observation, let the agent decide next step. Example: Tool returns "API rate limited". Agent thinks "API is busy, wait 1 second and retry" or "use alternative tool."

**Q9: What safety measures do agents need?**
A: 1) Confirmation for destructive actions (delete, spend money), 2) Tool permissions (read-only by default), 3) Content filtering, 4) Human handoff for decisions, 5) Logging all actions. Example: Agent asks "Are you sure you want to delete all records?" before executing.

**Q10: What are the best frameworks for building agents?**
A: LangChain (most popular, flexible), LangGraph (graph-based, complex workflows), AutoGen (Microsoft, multi-agent), CrewAI (role-based agents), OpenAI Assistants API (managed). Example: CrewAI for "CEO + Researcher + Writer" agent teams.

**Q11: How do agents handle long-running tasks?**
A: Async execution with status updates. Example: Agent reports "Step 1/5: Searching database... Step 2/5: Analyzing results..." User can check progress and cancel if needed. WebSocket for real-time updates.

**Q12: What is the difference between autonomous and semi-autonomous agents?**
A: Autonomous agents act without human input (risky but fast). Semi-autonomous ask for confirmation on key decisions (safer but slower). Example: Research assistant is autonomous (search only). Purchase assistant asks "Shall I buy this?"
