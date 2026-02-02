# TRP 1 - MCP Setup Challenge Report

## 1. What I Did
I transformed my VS Code environment into an "AI Orchestrator" by configuring the Tenx MCP server and implementing a robust rules file based on Boris Cherny's best practices.

* **Rules Engineering**: I created a `.github/copilot-instructions.md` file that fundamentally changes how the agent behaves.
* **"Plan Mode" Implementation**: I added a strict rule forcing the agent to output a bulleted plan before writing code for complex tasks.
* **Anti-Hallucination**: I implemented a "Never Speculate" rule, requiring the agent to read files before answering questions about them.
* **Workflow Integration**: I configured mandatory triggers (`log_passage_time_trigger`) to ensure all interactions are logged for analysis.

## 2. What Worked
My testing confirmed that the rules successfully controlled the agent's behavior:

* **Plan Mode (Test 1)**: When asked to "refactor my project," the agent **did not** output code immediately. Instead, it produced a structured 4-step plan ("Analyze Current Structure", "Create New Folders", etc.), proving the "Think Before You Code" rule works.
* **Tone & Style (Test 2)**: The agent answered "The capital of Ethiopia is Addis Ababa" directly and concisely, adhering to the "2026 Human" persona without robotic filler words.
* **Fact-Checking (Test 3)**: When asked about a non-existent file (`auth_config.json`), the agent correctly used its search tool, found nothing, and stated "It seems there is no auth_config.json file" instead of making up (hallucinating) its contents.

## 3. Challenges & Troubleshooting
* **MCP Connection Stability**: During the initial run of Test 1, the agent reported an "issue with the logging tool."
    * **Troubleshooting**: I verified the `.vscode/mcp.json` configuration to ensure the `X-Device` header was set to "linux". I also restarted the VS Code window to force a fresh connection handshake.
    * **Result**: A re-test confirmed the error was resolved. The agent successfully generated the plan without any logging errors, verifying a stable connection.
* **VS Code Limitations**: Unlike Claude Code, VS Code doesn't have a native "Plan Mode" button.
    * **Solution**: I simulated this behavior by adding a high-priority rule in the `copilot-instructions.md` file that explicitly forbids generating code without a plan for complex prompts.

## 4. Insights Gained
* **Rules as "Cognitive Guardrails"**: I learned that we can program the AI's *process*, not just its output. By forcing a "Plan" step, we prevent the agent from getting stuck in bad implementation paths.
* **The Power of "Negative Constraints"**: Rules like "Never Speculate" are just as powerful as positive instructions. Forcing the agent to admit ignorance (Test 3) is safer than letting it guess.
* **Iterative Memory**: The "Mistake Protocol" I added ensures that the agent improves over time by updating its own rules file when it fails, treating the documentation as a living memory system.