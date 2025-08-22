## **PerlAI Router Workflow (Current)**

This outlines the current, refined process for handling an incoming request. The core idea is to enrich a user's prompt with context from specialized agents before sending it to a primary LLM for the final response.

### **1\. load\_agents**

* **Purpose**: To dynamically discover and register all available agents on startup.  
* **Process**:  
  * Scans the ./lib/PerlAI/Agent/ directory for .pm files.  
  * For each file, it loads the module and reads its metadata:  
    * name: A human-readable name for the agent.  
    * description: A brief explanation of what the agent does.  
    * trigger: A Perl regular expression (qr/.../) used for fast, simple matching.  
    * function: A reference to the subroutine that contains the agent's logic.  
  * Stores the loaded agent metadata in a global hash for the router to use.

### **2\. parse\_input (formerly validate\_input and parse\_input)**

* **Purpose**: To analyze the user's prompt, ensure it's appropriate, and determine which agent(s) should be run.  
* **Process**:  
  * **(Future)** **Appropriateness Check**: An initial, low-cost LLM call will be made to verify the prompt is business-appropriate and in scope. If not, the process is halted.  
  * **Hybrid Routing**:  
    1. **Regex First**: It first attempts to match the user's prompt against the trigger regex of each loaded agent. This is a fast path for simple, unambiguous requests (e.g., "what time is it?").  
    2. **(Future)** **LLM Fallback**: If no regex trigger matches, it will then make a second LLM call. This call will include the list of available agents and their descriptions, asking the LLM to choose the best agent and provide a query for it. The LLM will be instructed to respond in a structured JSON format.  
  * **Output**: This step populates the agents\_to\_run list within the central $request\_state hash.

### **3\. gather\_data**

* **Purpose**: To execute the agents selected in the parse\_input step and add their findings to the request as context.  
* **Process**:  
  * It iterates through the agents\_to\_run list.  
  * For each agent, it calls the associated function.  
  * Each agent's function takes the $request\_state hash, performs its task (e.g., getting the current time), adds its findings as a new "system" message to the messages array within the final\_payload, and returns the modified state hash.

### **4\. generate\_final\_response**

* **Purpose**: To take the original prompt and all the newly gathered context and send it to the primary LLM for a synthesized answer.  
* **Process**:  
  * It constructs a final payload using the messages array (which now includes the agent context).  
  * It forwards this complete payload to a configured LLM server (e.g., a local Llama instance).  
  * It streams the LLM's response directly back to the end client.

### **5\. verify\_output (Future)**

* **Purpose**: To perform a final quality check on the LLM's generated response.  
* **Process**: The generated text will be sent to an LLM one last time with instructions to check for appropriateness, tone, and factual consistency before it's sent to the user.

### **6\. format\_output (Future)**

* **Purpose**: To apply any final formatting to the response.  
* **Process**: Based on client request or configuration, this step will format the text (e.g., with Markdown) before the stream is finalized.  
