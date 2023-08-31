# How I use AI to code

For the last couple months I've been using AI extensively to write code, especially for backend tasks. I'm using [Kamara](https://kamaraapp.com/), a VS Code extension I created that uses GPT-4 to write and edit code directly in the editor.  
  
## The Task at Hand

I'm working on a platform that helps easily create & monetize courses.  
  
Up until now there was a single instance of a course in my database, so the teacher couldn't make changes without students seeing them. The feature I wanted to add is to allow teachers to work on a draft and choose when to publish all their changes to their students.  
  
First step is coming up with the high-level solution. I do this myself, without relying on AI for it. I found that the AI is excellent at writing code but not so much at deciding what code it should write, so I make things as easy as possible for it.  
  
The solution I landed on was to consider the existing instance of the course the draft and to make a deep copy of it when publishing. I'll create a new column "published_course_id" to the draft course (will be NULL for live courses)  so I know what the published one is. Teachers can keep making changes to the draft until they are happy with results and we'll update the "published_course_id" column when they publish and make a new copy.  
  
Time to code! Here's where the AI will do the heavy lifting. It took three prompts to implement this:  

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/how-i-use-ai-to-code/prompt_migration.png" alt="Prompt to run a migration">

This was an easy one, got it right first try using Knex, the query builder I use to interact with the Postgres database and wrote a new file in the right place:  

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/how-i-use-ai-to-code/result_migration.png" alt="Code writting by AI for the migration">

Second (and most difficult) one. I try to be direct and detailed. I've found using proper grammar is not as important as making sure all details are included and explained clearly so I sometimes repeat words a few times if it helps with clarity, in this example the word "course":

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/how-i-use-ai-to-code/prompt_drafts_endpoint.jpeg" alt="Prompt to write the draft endpoint">

The index change was wrong, the AI wrote random code because it hadn't read the file (so it didn't have it in its context!) but it was a one-liner so I just reverted that change and added it by hand.  
  
The drafts router it got almost right:  

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/how-i-use-ai-to-code/result_migration.png" alt="The draft endpoint implementation">

I made two small changes to the output:  
- I added the moduleMapping and sectionMapping variables manually. The AI didn't write them so it was using the draft ids for those entities. This was caught by the test that was written later!
- Added the asyncPools as an optimization so it creates new rows in parallel. It was creating all entities sequentially before. I could have also made everything happen in a single transaction, but didn't for now. This is something that the AI could do itself probably.  
  
Now for the testing part, I'm not a fan of unit tests, I much prefer integration tests so that's the kind I have in this project. It uses a real Postgres database and absolutely no mocks. Here's the prompt:  

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/how-i-use-ai-to-code/prompt_test.png" alt="Prompt to write an integration test">

Shortly after the entire test was written. It was a bit low on assertions so I added a couple manually (Copilot helped though). I don't usually write tests for MVPs but this was too convenient, it took just two minutes to have a full e2e test that caught the error I mentioned before which allowed me to quickly fix it.  
  
## Closing Thoughts

I don't think the current state of AI is good at deciding how to implement something in existing codebases yet but if you tell it what to do it's excellent at actually writing the code for it. You just have to be very explicit in your instructions.  
  
Here's a final example that illustrates this. In the courses there's "sections" and each section can have several "blocks". I wanted to write a new endpoint that could reorder the blocks in a section. Here's the prompt:  

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/how-i-use-ai-to-code/prompt_blocks.png" alt="Prompt to reorder blocks">

And the result:

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/how-i-use-ai-to-code/result_blocks.png" alt="Code for endpoint that reorders blocks">

The only thing I needed to add was the userHasWriteAccess line. I forgot to add it to the prompt! Notice that in the previous example I did ask for auth and it implemented it correctly, but in the new one I didn't mention it so it didn't add it. You need to be very explicit with what you want the AI to do, it's just a tool in the end!  
  
The AI isn't taking over the entire development process here, it is just massively accelerating the code-writing part of it.  
  
This is just going to get better from here, I haven't had as much luck using AI effectively for UI-related tasks yet, but backend dev has become a breeze with this, looking forward to higher context windows and AIs with a good eye for design somehow.
