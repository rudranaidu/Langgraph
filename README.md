https://github.com/jkmaina/LangGraphProjects


# LangGraph Agent Cheat Sheet 🚀

## Core Thinking Framework

```
State → Nodes → Entry → Edges → Conditional → END
```

---

# 1. Simple RAG Agent

## Graph

```
search_docs → generate_answer → END
```

# LangGraph Agent Cheat Sheet 🚀

## Core Thinking Framework

```
State → Nodes → Entry → Edges → Conditional → END
```

---

# 1. Simple RAG Agent

## Graph

```
search_docs → generate_answer → END
```

Graph Representation

 🧠 1. State
    input
    docs
    output
    steps

🧠 2. Nodes
    search_docs → retrieves documents
    generate_answer → generates final answer

🧠 3. Entry Point

    Entry: search_docs

🧠 4. Edges

    search_docs → generate_answer → END

🧠 5. Conditional Edges

    Not required

🧠 6. Stop Condition

    After generate_answer → END

## Code

```python
from typing import TypedDict
from langgraph.graph import StateGraph, END

class State(TypedDict):
    input: str
    docs: str
    output: str

def search(state):
    return {"docs": "LangChain is a framework"}

def generate(state):
    return {"output": f"Answer: {state['docs']}"}

g = StateGraph(State)
g.add_node("search", search)
g.add_node("generate", generate)

g.set_entry_point("search")
g.add_edge("search", "generate")
g.add_edge("generate", END)

app = g.compile()
```

---

# 2. Conditional Routing Agent

## Graph

```
        decide
       /     \
  calculate   search
       \     /
         END
```

## Code

```python
class State(TypedDict):
    input: str
    decision: str
    output: str

def decide(state):
    return {"decision": "math" if "calculate" in state["input"] else "search"}

def calc(state):
    return {"output": "4"}

def search(state):
    return {"output": "AI explanation"}

def route(state):
    return state["decision"]

g = StateGraph(State)
g.add_node("decide", decide)
g.add_node("calc", calc)
g.add_node("search", search)

g.set_entry_point("decide")

g.add_conditional_edges("decide", route, {
    "math": "calc",
    "search": "search"
})

g.add_edge("calc", END)
g.add_edge("search", END)

app = g.compile()
```

---

# 3. Logs → Analyze → Fix

## Graph

```
fetch_logs
 /        \
yes       no
 |         |
analyze    END
 |
fix
 |
END
```

## Code

```python
class State(TypedDict):
    input: str
    logs: str
    analysis: str
    output: str

def fetch(state):
    logs = "error log" if "error" in state["input"] else None
    return {"logs": logs}

def analyze(state):
    return {"analysis": "issue identified"}

def fix(state):
    return {"output": "restart service"}

def check(state):
    return "yes" if state["logs"] else "no"

g = StateGraph(State)
g.add_node("fetch", fetch)
g.add_node("analyze", analyze)
g.add_node("fix", fix)

g.set_entry_point("fetch")

g.add_conditional_edges("fetch", check, {
    "yes": "analyze",
    "no": END
})

g.add_edge("analyze", "fix")
g.add_edge("fix", END)

app = g.compile()
```

---

# 4. Loop + Retry Agent

## Graph

```
search → evaluate
   ↑        ↓
   └── retry ──┘
        |
       END
```

## Code

```python
class State(TypedDict):
    input: str
    count: int
    output: str

def search(state):
    return {"output": "partial", "count": state["count"] + 1}

def evaluate(state):
    return "done" if state["count"] >= 2 else "retry"

g = StateGraph(State)
g.add_node("search", search)
g.add_node("eval", evaluate)

g.set_entry_point("search")
g.add_edge("search", "eval")

g.add_conditional_edges("eval", evaluate, {
    "retry": "search",
    "done": END
})

app = g.compile()
```

---

# 5. Tool Failure + Fallback

## Graph

```
tool
 /   \
ok   fail
 |     |
END fallback
        |
       END
```

## Code

```python
class State(TypedDict):
    input: str
    status: str
    output: str

def tool(state):
    if "fail" in state["input"]:
        return {"status": "fail"}
    return {"status": "ok", "output": "data"}

def route(state):
    return state["status"]

def fallback(state):
    return {"output": "fallback"}

g = StateGraph(State)
g.add_node("tool", tool)
g.add_node("fallback", fallback)

g.set_entry_point("tool")

g.add_conditional_edges("tool", route, {
    "ok": END,
    "fail": "fallback"
})

g.add_edge("fallback", END)

app = g.compile()
```

---

# 6. Multi-Tool Chain

## Graph

```
search
 /   \
yes   no
 |     |
summ   END
 |
END
```

## Code

```python
class State(TypedDict):
    input: str
    news: str
    output: str

def search(state):
    news = "AI news" if "AI" in state["input"] else None
    return {"news": news}

def summarize(state):
    return {"output": f"Summary: {state['news']}"}

def check(state):
    return "yes" if state["news"] else "no"

g = StateGraph(State)
g.add_node("search", search)
g.add_node("summarize", summarize)

g.set_entry_point("search")

g.add_conditional_edges("search", check, {
    "yes": "summarize",
    "no": END
})

g.add_edge("summarize", END)

app = g.compile()
```

---

# 🚀 Final Tip

Always think:

```
State → Nodes → Entry → Edges → Conditional → END
```


## Code

```python
from typing import TypedDict
from langgraph.graph import StateGraph, END

class State(TypedDict):
    input: str
    docs: str
    output: str

def search(state):
    return {"docs": "LangChain is a framework"}

def generate(state):
    return {"output": f"Answer: {state['docs']}"}

g = StateGraph(State)
g.add_node("search", search)
g.add_node("generate", generate)

g.set_entry_point("search")
g.add_edge("search", "generate")
g.add_edge("generate", END)

app = g.compile()
```

---

# 2. Conditional Routing Agent

## Graph

```
        decide
       /     \
  calculate   search
       \     /
         END
```

## Code

```python
class State(TypedDict):
    input: str
    decision: str
    output: str

def decide(state):
    return {"decision": "math" if "calculate" in state["input"] else "search"}

def calc(state):
    return {"output": "4"}

def search(state):
    return {"output": "AI explanation"}

def route(state):
    return state["decision"]

g = StateGraph(State)
g.add_node("decide", decide)
g.add_node("calc", calc)
g.add_node("search", search)

g.set_entry_point("decide")

g.add_conditional_edges("decide", route, {
    "math": "calc",
    "search": "search"
})

g.add_edge("calc", END)
g.add_edge("search", END)

app = g.compile()
```

---

# 3. Logs → Analyze → Fix

## Graph

```
fetch_logs
 /        \
yes       no
 |         |
analyze    END
 |
fix
 |
END
```

## Code

```python
class State(TypedDict):
    input: str
    logs: str
    analysis: str
    output: str

def fetch(state):
    logs = "error log" if "error" in state["input"] else None
    return {"logs": logs}

def analyze(state):
    return {"analysis": "issue identified"}

def fix(state):
    return {"output": "restart service"}

def check(state):
    return "yes" if state["logs"] else "no"

g = StateGraph(State)
g.add_node("fetch", fetch)
g.add_node("analyze", analyze)
g.add_node("fix", fix)

g.set_entry_point("fetch")

g.add_conditional_edges("fetch", check, {
    "yes": "analyze",
    "no": END
})

g.add_edge("analyze", "fix")
g.add_edge("fix", END)

app = g.compile()
```

---

# 4. Loop + Retry Agent

## Graph

```
search → evaluate
   ↑        ↓
   └── retry ──┘
        |
       END
```

## Code

```python
class State(TypedDict):
    input: str
    count: int
    output: str

def search(state):
    return {"output": "partial", "count": state["count"] + 1}

def evaluate(state):
    return "done" if state["count"] >= 2 else "retry"

g = StateGraph(State)
g.add_node("search", search)
g.add_node("eval", evaluate)

g.set_entry_point("search")
g.add_edge("search", "eval")

g.add_conditional_edges("eval", evaluate, {
    "retry": "search",
    "done": END
})

app = g.compile()
```

---

# 5. Tool Failure + Fallback

## Graph

```
tool
 /   \
ok   fail
 |     |
END fallback
        |
       END
```

## Code

```python
class State(TypedDict):
    input: str
    status: str
    output: str

def tool(state):
    if "fail" in state["input"]:
        return {"status": "fail"}
    return {"status": "ok", "output": "data"}

def route(state):
    return state["status"]

def fallback(state):
    return {"output": "fallback"}

g = StateGraph(State)
g.add_node("tool", tool)
g.add_node("fallback", fallback)

g.set_entry_point("tool")

g.add_conditional_edges("tool", route, {
    "ok": END,
    "fail": "fallback"
})

g.add_edge("fallback", END)

app = g.compile()
```

---

# 6. Multi-Tool Chain

## Graph

```
search
 /   \
yes   no
 |     |
summ   END
 |
END
```

## Code

```python
class State(TypedDict):
    input: str
    news: str
    output: str

def search(state):
    news = "AI news" if "AI" in state["input"] else None
    return {"news": news}

def summarize(state):
    return {"output": f"Summary: {state['news']}"}

def check(state):
    return "yes" if state["news"] else "no"

g = StateGraph(State)
g.add_node("search", search)
g.add_node("summarize", summarize)

g.set_entry_point("search")

g.add_conditional_edges("search", check, {
    "yes": "summarize",
    "no": END
})

g.add_edge("summarize", END)

app = g.compile()
```

---

# 🚀 Final Tip

Always think:

```
State → Nodes → Entry → Edges → Conditional → END
```
