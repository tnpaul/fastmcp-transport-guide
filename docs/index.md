# FastMCP Transport Options (v2.0+)

FastMCP provides multiple **transport options** to connect MCP clients and servers. Each transport type is suited for different environments and communication patterns.

Below is a complete guide to all available transport types, along with **ready-to-use Python examples** for both the **server** and **client**.

---

## 1. `stdio` Transport

### When to Use

* Ideal for **local development** or when running as a **child process**.
* Low-latency communication using standard input/output streams.
* Great for CLI tools or desktop apps.

### Server (stdio)

```python
# server_stdio.py
from fastmcp import FastMCP

# Create MCP server instance
mcp = FastMCP("STDIO Demo Server")

@mcp.tool()
def greet(name: str) -> str:
    """Greet someone by name."""
    return f"Hello, {name}!"

@mcp.tool()
def calculate(op: str, a: float, b: float) -> str:
    """Perform basic arithmetic operations."""
    operations = {
        "plus": a + b,
        "minus": a - b,
        "multiply": a * b,
        "divide": a / b if b != 0 else "Error: Division by zero"
    }
    
    if op not in operations:
        return f"Error: Unsupported operation '{op}'. Use: plus, minus, multiply, divide"
    
    result = operations[op]
    return f"{a} {op} {b} = {result}"

if __name__ == "__main__":
    # Run with STDIO transport
    mcp.run(transport="stdio")
```

### Client (stdio)

```python
# client_stdio.py
import asyncio
from fastmcp import Client

async def main():
    client = Client("stdio_server.py")
    
    async with client:
        print("FastMCP STDIO Client")

        # 1. List tools
        tools_list = await client.list_tools()
        print("\nAvailable tools:")
        for tool in tools_list:
            print(f" - {tool.name}: {tool.description}")
            print(f"   Input schema: {tool.inputSchema}")

        # 2. Call greet tool
        name = "Fast MCP Demo"
        result = await client.call_tool("greet", arguments={"name": name})
        
        # Extract and print result nicely
        if result.content:
            print(f"\nTool returned: {result.content[0].text}")
        else:
            print("\nTool returned no content!")

if __name__ == "__main__":
    asyncio.run(main())
```

### Run

```bash
python client_stdio.py
```

---

## 2. `sse` (Server-Sent Events) Transport

### When to Use

* Ideal for **long-lived connections** over HTTP.
* Suitable for **browser-based clients** or when you need a **single connection streaming multiple events**.

### Server (sse)

```python
# server_sse.py
from fastmcp import FastMCP

# Create MCP server instance
mcp = FastMCP("SSE Demo Server")

@mcp.tool()
def greet(name: str) -> str:
    """Greet someone by name."""
    return f"Hello, {name}!"

@mcp.tool()
def calculate(op: str, a: float, b: float) -> str:
    """Perform basic arithmetic operations."""
    operations = {
        "plus": a + b,
        "minus": a - b,
        "multiply": a * b,
        "divide": a / b if b != 0 else "Error: Division by zero"
    }
    
    if op not in operations:
        return f"Error: Unsupported operation '{op}'. Use: plus, minus, multiply, divide"
    
    result = operations[op]
    return f"{a} {op} {b} = {result}"

if __name__ == "__main__":
    # Run with SSE transport
    mcp.run(transport="sse", host="127.0.0.1", port=8000)
```

### Client (sse)

```python
# client_sse.py
import asyncio
from fastmcp import Client

async def main():
    client = Client("http://127.0.0.1:8000/sse")
    
    async with client:
        print("FastMCP SSE Client")

        # 1. List tools
        tools_list = await client.list_tools()
        print("\nAvailable tools:")
        for tool in tools_list:
            print(f" - {tool.name}: {tool.description}")
            print(f"   Input schema: {tool.inputSchema}")

        # 2. Call greet tool
        name = "Fast MCP Demo"
        result = await client.call_tool("greet", arguments={"name": name})
        
        # Extract and print result nicely
        if result.content:
            print(f"\nTool returned: {result.content[0].text}")
        else:
            print("\nTool returned no content!")

if __name__ == "__main__":
    asyncio.run(main())
```

### Run

```bash
python server_sse.py
python client_sse.py
```

---

## 3. `http` Transport

### When to Use

* Best for **stateless, request/response** style communication.
* Use when you only need **single-shot tool calls** (no persistent connection).

### Server (http)

```python
# server_http.py
from fastmcp import FastMCP

# Create MCP server instance
mcp = FastMCP("HTTP Demo Server")

@mcp.tool()
def greet(name: str) -> str:
    """Greet someone by name."""
    return f"Hello, {name}!"

@mcp.tool()
def calculate(op: str, a: float, b: float) -> str:
    """Perform basic arithmetic operations."""
    operations = {
        "plus": a + b,
        "minus": a - b,
        "multiply": a * b,
        "divide": a / b if b != 0 else "Error: Division by zero"
    }
    
    if op not in operations:
        return f"Error: Unsupported operation '{op}'. Use: plus, minus, multiply, divide"
    
    result = operations[op]
    return f"{a} {op} {b} = {result}"

if __name__ == "__main__":
    # Run with HTTP transport
    mcp.run(transport="http", host="127.0.0.1", port=8000)
```

### Client (http)

```python
# client_http.py
import asyncio
from fastmcp import Client

async def main():
    client = Client("http://127.0.0.1:8000/mcp")
    
    async with client:
        print("FastMCP HTTP Client")

        # 1. List tools
        tools_list = await client.list_tools()
        print("\nAvailable tools:")
        for tool in tools_list:
            print(f" - {tool.name}: {tool.description}")
            print(f"   Input schema: {tool.inputSchema}")

        # 2. Call greet tool
        name = "Fast MCP Demo"
        result = await client.call_tool("greet", arguments={"name": name})
        
        # Extract and print result nicely
        if result.content:
            print(f"\nTool returned: {result.content[0].text}")
        else:
            print("\nTool returned no content!")

if __name__ == "__main__":
    asyncio.run(main())
```

### Run

```bash
python server_http.py
python client_http.py
```

---

## 4. `streamable http` (Experimental)

### When to Use

* Similar to `http` but allows **streaming results** back to the client.
* Useful when tool output is **large or incremental** (e.g., logs, progress updates).
* **Note:** May still be experimental in some versions — check release notes.

### Server (streamable http)

```python
# server_stream_http.py
<To be added>
```

### Client (streamable http)

```python
# client_stream_http.py
<To be added>
```

### Run

```bash
python server_stream_http.py
python client_stream_http.py
```

---

## CLI Usage

FastMCP also provides a CLI for running servers without writing client code:

```bash
# Run with STDIO (default)
mcp run server_stdio.py

# Run with SSE  
mcp run --transport sse --host 127.0.0.1 --port 9000 server_sse.py

# Run with HTTP
mcp run --transport http --host 127.0.0.1 --port 8000 server_http.py

```

---

## Summary Table

| Transport Type      | Connection Style       | Best For                           |
| ------------------- | ---------------------- | ---------------------------------- |
| **stdio**           | Local pipes            | CLI tools, local dev               |
| **sse**             | Persistent HTTP stream | Browser clients, real-time updates |
| **http**            | Stateless HTTP         | Simple, one-off calls              |
| **streamable http** | Streaming over HTTP    | Large/streamed responses           |

---

## Best Practices

1. **Development**: Use STDIO for quick prototyping and local development
2. **Production**: Use HTTP transport for web services and production deployments  
3. **Legacy**: Only use SSE if you need to maintain backward compatibility
4. **Error Handling**: Always wrap client calls in try-catch blocks for production code
5. **Resource Management**: Use `async with client:` to ensure proper cleanup

## Requirements

Make sure you have FastMCP installed:

```bash
pip install fastmcp>=2.0
```
---

✅ **Tip:** Always match the transport option in both **server** and **client** to avoid connection errors. Make sure `fastmcp` ≥ 2.0 is installed before running these examples.
