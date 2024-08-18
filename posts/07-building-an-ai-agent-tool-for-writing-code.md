# Building an AI-Agent Tool for Writing Code

I built an AI-agent-based tool (Electron app) to write code for me. Some agents for my (TypeScript) backend codebase are:  

**SQL migrations:** Writes database migrations and type files.  
**Services:** Writes the services that interact with the database and process the data in any way that is needed.  
**API routes:** Validates the input from the HTTP request, does auth checks, then calls the right service.  
**Tester:** Writes integration tests for endpoints.  

Some information is shared between all of them, like the file structure, the content of the type files (which are auto-added to context if a file that imports one is in the context), what the project does, the database schema, while other agent-specific information and instructions are unique to each agent.

For example, the **API Routes** agent knows to use Zod for validation. It also uses the *apiHandler* wrapper, which handles parsing, auth, and error handling, has a couple of examples and follows project-specific directions. Now, if I just write *"Create a POST /calendar/event endpoint that calls the CalendarService"* it knows exactly where it needs to put the file, what the required and optional fields and their types are (from reading the CalendarService), and ensures authentication is being handled. This agent writes "perfect" code (according to the codebase's standards) almost every time for a simple prompt like the one above.

What's more, I can then reply to the **Tester** agent to *"Write tests for it"* and it will understand that 'it' refers to the POST /calendar/event endpoint, have full context from the previous task and have specific instructions on how to structure and write tests, where the helper functions are, etc to be able to write tests that fit in with the rest of the codebase.

These agents are all tailored to my codebase, to the way I personally do things, but the prompts can be easily edited from the UI to fit different codebases or programming styles.

My current workflow for writing a new feature using the tool is to send a prompt to the **Migrations** agent with the table I want to create or columns to add to a table. Then, I ask the **Services** agent to create or edit an existing service (I usually mention it by name). The **API Routes** agent hooks up the service to the Express router by creating a new endpoint, and finally, the **Tester** agent writes tests for it. It takes me at least 4 prompts, even if they are simple ones.

My next step is having a **Project Manager** agent that knows about all these agents and can delegate work to the right one in the correct order, build the app, run tests and use a browser to navigate the website to check that it works. This will allow me to write a single shorter prompt that just describes the feature and have the system of agents build it with less supervision. I will still do a code review, but I find it easier to review the entire feature at once rather than reviewing each step individually. This approach is also faster than coding everything by hand. I can imagine a future where I can kick off different agents with different tasks and have each open a PR with the solution a few minutes later.
