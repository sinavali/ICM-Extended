# 1. Introduction

There are genuinely good agentic frameworks available today. CrewAI, LangChain, AutoGen, and others handle multi-step orchestration, memory management, tool use, and error recovery. They work. But they work within their own structures, and adjusting those structures requires development work. Changing the order of steps, swapping a prompt, adding or removing a stage, skipping something that is not relevant today: these actions typically mean editing code, understanding abstractions, and redeploying. For practitioners whose workflows are sequential and need human review at each step, the control surface can be much simpler.

This paper describes Interpretable Context Methodology (ICM), a method for orchestrating AI agent workflows using folder structure, markdown files, and local scripts. The central observation is straightforward: if the prompts and context for each stage of a workflow already exist as files in a well-organized folder hierarchy, you do not need a coordination framework to manage multiple specialized agents. You need one orchestrating agent that reads the right files at the right moment. The folder structure tells it what to do at each step, and if the agent delegates sub-tasks, the same folder structure determines what context those sub-agents receive. Local Python scripts handle the parts that do not need AI: fetching data, moving files, formatting output, sending emails.

This is going backward before going forward. The principles that made Unix pipelines effective in the 1970s[^2] and multi-pass compilers tractable in the 1980s apply directly to AI agent orchestration in the 2020s. ICM applies those principles to the specific challenge of structuring context for language models.

The central question this paper examines is how structuring the context delivery mechanism as a filesystem hierarchy affects practitioners’ ability to control, inspect, and edit AI agent behavior across multi-step workflows, and what this structure means for the quality of the model’s output at each stage.

The paper is organized as follows. Section 2 traces the relevant background across software engineering, context engineering, and human oversight research. Section 3 describes the protocol itself. Section 4 walks through working implementations and reports on early practitioner experience. Section 5 discusses where this approach fits and where it does not, including implications for the design of interactive intelligent systems more broadly. Section 6 explores future directions, drawing on the structural parallels between ICM and multi-pass compilation to propose semantic debugging and source-level traceability for AI workflows.

**Table 1.** Comparison of control surfaces for sequential, human-reviewed workflows. The first six rows show dimensions where ICM’s filesystem approach simplifies common operations. The last four rows show dimensions where framework-based approaches provide capabilities that ICM lacks or handles less well.

|  Dimension  |  Framework approach  |  ICM approach  |
| --- | --- | --- |
|  Change stage order  |  Edit orchestration code, redeploy  |  Rename or reorder folders  |
|  Modify a prompt  |  Edit agent configuration in code  |  Edit a markdown file  |
|  Add or remove a stage  |  Write new agent class, update orchestrator  |  Add or delete a folder  |
|  Inspect intermediate state  |  Add logging, build dashboard  |  Open the folder, read the files  |
|  Hand off to another person  |  Document environment, dependencies, setup  |  Copy the folder  |
|  Who can make changes  |  Developer  |  Anyone with a text editor  |
|  Error recovery mid-pipeline  |  Built-in retry, fallback, exception handling  |  Manual re-run of failed stage  |
|  Conditional branching  |  Programmatic routing based on agent output  |  Human decides between stages  |
|  Concurrent execution  |  Native parallel agent coordination  |  Sequential by design  |
|  External service integration  |  Programmatic API calls, auth management  |  Local scripts or MCP connections  |

[^2]: Programs that do one thing. Output of one becomes input of another. Plain text as universal interface. These ideas are over fifty years old and they hold up.
