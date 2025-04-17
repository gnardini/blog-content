# How AI Coding Agents Work (April 2025) 

Every AI coding assistant like Cursor, Windsurf, Claude Code, Codex, and many more all follow the same two‑step dance: **gather context, then write or edit code**.

## Gather context

### Open specific files
The simplest tool is a `readFile(path)` call. The agent requests the file the user mentions (“open server.ts”) or chooses one itself after a search.

### grep‑style search
A fast search tool like `ripgrep` can query the codebase using regex or exact strings and return raw lines plus file paths. LLMs are good at writing regex and this helps them get relevant context when searching for specific functions or phrases.

### Embeddings
Many of these tools index the codebase when first interacted with and store embeddings of it in a vector database. Embeddings represent code as a vector of numbers. Indexing involves splitting up the code into small chunks and storing those chunks.

Then when a user makes a query, we can create the embedding of the query and find the K chunks in the database that are the most similar to it. This can help find relevant files using natural language.

### Structural operations
Parsing the Abstract Syntax Tree (AST) lets the agent jump to symbol definitions, build call graphs, or rename variables. The [Tree Sitter](https://tree-sitter.github.io/tree-sitter/) library is the most used one for this.

### MCP (Model Context Protocol)
MCP servers expose tools that can fetch external data like database queries, documentation, or API docs as easily as it can open files.

### Rule‑based docs
Agents can read documentation (e.g. `RULES.md` or files in `.cursor/rules`) to have improved context on the codebase.

### Memory
Some agents can store memory from past interactions to learn from it. For example, if a user mentions to use a specific `logger` class for logging, the agent can store that info into memory to apply it in future work.

## Write code

There are two main ways for agents to make changes to a codebase.

### Full-file generation
The default way when creating new files and can be used to make edits too if making big changes. It's wasteful and slow to write an entire file if you just want to change one line though. 

When using this to make edits, a common problem is the agent typing `// … existing code here` which would delete large chunks of the file.

### Diff / patch editing
The agent outputs the exact content of lines to delete and the content of the new lines to replace them. Similar to how git diffs work.

For example, a diff could look like this:

```
-  const token = jwt.sign(payload, OLD_SECRET, { expiresIn: '1h' });
+  // rotate to the new secret; matches env var NEW_JWT_SECRET
+  const token = jwt.sign(payload, NEW_SECRET, { expiresIn: '15m' });
```

This is faster for small changes, the problems could arise if the deleted lines aren't found in the file being edited or if they appear multiple times. If this method is used to edit >50% of the file, doing the full-file generation would have been faster since we have to write both the deleted and the added lines here, while full-file generation only writes the new content of the file.

## Validation

After every patch some agents kick off the same steps a human would:
 1. Run the build step to make sure “it compiles”.
 2. Lint and format to catch style violations.
 3. Run unit / integration tests (npm test, pytest, etc.).

If any of these fail, the failure can be passed back to the LLM for fixing.

## Conclusion

There's an arms race right now going on to try and build the first true autonomous agent that can make meaningful contributions in real codebases. Tools are getting good, but they are still only human helpers, not replacements. Only time will tell if the latter will be possible.
