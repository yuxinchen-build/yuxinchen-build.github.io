---
title: "Agentic AI course notes from deeplearning.ai"
description: "My notes while taking Andrew Ng's Agentic AI class"
pubDate: 2026-03-15
---

## Module 3 Tool use
Tools are just code that the LLM can request to be executed. When you prompt the LLM and the LLM can decide if it needs a tool and looks through available tools. **LLMs can choose tools when approapriate.**

### Examples
- Find restaurants near my area: web_search
- Show me customers who bought white sunglasses: query_database
- How much money will i have after 10 years if I deposit %500 at 5% interest: interest_calc

### Multiple tools
Example: find a free slot on Thursday in my calendar and make an appoinment with Alice. 
Tools to use: 
- make_appointment
- check_calendar
- delete_appointment

### Creating a tool
#### Prompting an LLM to use tools
The system prompt might look like: You havea ccess to a tool called get_current_time. To use it, return the following exactly: FUNCTION: get_current_time()
So when the user asks "what time is it", then the LLM outputs FUNCTION: get_current_time(). Then if "FUNCTION" in output: , program the LLM to pull the function get_current_time(). 
**The LLM doesn't call the function directly.** It instead outputs something in a specific format like FUNCTION: get_current_time(), which tells the developer to call the function for the LLM, and then tell the LLM what was the output of the function. Modern LLMs are trained with specific syntax to call tools. 

### Tool syntax
Behind the scenes, the agent SDK will create tool details in JSON format and it will call the tool for us. 

[aisuite](https://github.com/andrewyng/aisuite) is a very helpful library that unifies multiple GenAI providers. 

### Code execution
Prompt LLM to write the code, the developer execute codes and pass the result to LLM to surface to the end user. 

## Module 4 Practical tips for building agentic AI
Build a quick system in a safe way and use this initial prototype to iterate. 

## Module 5 Patterns for highly autonomous agents
Multi agent workflows: rather than providing multiple tools, provide multiple agents. System prompt becomes "you have the following team of agents tow work with..."

Key design decision: what is the communication pattern between multiple agents?

### Communication patterns for multi-agent systems
* Linear
* Hierarchical: A manager communicates with other agents
* Deeper hierarchy: multiple layers in the organization (less often)
* All to all: anyone can talk to anyone. Prompt all agents that they can communicate with. (unpredicatble performance)