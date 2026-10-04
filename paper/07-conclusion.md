# 7. Conclusion

The principles that made Unix pipelines effective in the 1970s apply to AI agent orchestration in the 2020s. Programs that do one thing. Output of one becomes input of another. Plain text as universal interface. Human-readable intermediate state.

ICM applies these principles to a specific problem: structuring context for AI agents across multi-step workflows. The result is a system where the folder structure replaces the framework. One agent reads different context at each stage rather than multiple agents coordinating through code. Local scripts handle the mechanical work that does not need AI. Every intermediate output is a file a human can read and edit.

For practitioners whose AI workflows are sequential, reviewable, and repeatable, this means full pipeline capability with no framework to learn, no server to maintain, and no developer needed for day-to-day operation. The workspace is a folder. It can be copied, versioned, shared, and edited with a text editor. The simplest viable architecture for this class of problem is one that already exists on every computer: the filesystem.

The protocol is open source under the MIT license and includes a workspace-builder for creating new workspaces across any domain.
