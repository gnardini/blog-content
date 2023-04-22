# Creating My Personal Website With AI

Yeah, another AI code-writing post. I'll go with the disclaimer first, I'm working on a VS Code plugin called Kamara that writes code for you, you just need to review its output and make sure it works as intended. I thought this was a great project to show it off, so here I go.

## Background

I've been thinking about writing for a while. When I was a kid I used to love writing short stories, I did great at school and greatly enjoyed it. Unfortunately, as I grew older I decided to study Software Engineering where I didn't have writing classes anymore, so I stopped writing altogether.

Over the past few years I started many drafts, but never completed and even less published anything. I'm tempted to rationalize my past behavior, but I don't honestly know why I couldn't finish anything other than it's difficult. One of the lies I used to tell myself is that I didn't have anywhere to publish even if I did finish something. I created accounts in Hashnode, Medium, Gumroad, Substack and probably a few others but always found a silly problem that I didn't like about them. I used to tell myself that if I had my own blog everything would be easier.

Well, I published my first post on Substack last week, so I got over the initial hump. I like the website, but I also own the domain [https://gnardini.com](https://gnardini.com) and figured writing a blog with Kamara should be simple and gives me a nice thing to write about, so that's what I'm going to do. I know Substack has support for custom domains but I just don't want to pay them for the feature.


## Project setup

I know it's kind of overkill for a simple blog, but my main stack is Typescript, Next.js, Tailwind. I have a simple template I use on [https://github.com/gnardini/nextjs-template](https://github.com/gnardini/nextjs-template) so I'm going to go with that for familiarity's sake.

The website will have my name, my picture, a paragraph describing who I am and a chronological list of posts, with their title and date and a link that opens the blog in a new page. I will store the posts in markdown format in a different repo.

The first step is to clone the template and open VSCode with Kamara ready. Here's what I'm looking at:

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/personal-website/image10.png" alt=“Kickoff”>


## Start Building!

Time for the first instruction: "Create the landing page of my website. It should include my name, my picture, a paragraph describing who I am. Put all those things in one component and add a different Blog component, which should be empty for now."

Here's the first output:

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/personal-website/image6.png" alt="First Output">

And it created the index page like I asked it to

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/personal-website/image9.png" alt="Home Component">

[GitHub commit](https://github.com/gnardini/blog/commit/a83185dd52aa1914cdcabf6ae9c28fb21a35cc99)

Doesn't look great, but it did what I asked it to. I updated the name, image and paragraph manually and then a follow-up prompt: "In the Landing component, make the image 150x150, set a max width to the paragraph and add some more text below. It should mention the projects I worked on with a link and a short description. Add links for Celo Tracker ([https://celotracker.com](https://celotracker.com)) and ARAM Zone ([https://aram.zone](https://aram.zone)) Make up a description, I'll edit it"

After editing the text and minor touch up here's how it looks:

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/personal-website/image11.png" alt="First Output">

[GitHub commit](https://github.com/gnardini/blog/commit/a91020f8f8954d716eb005767296ea0e14c5f146)

I always hate what I write in my bios, it always sounds wrong. Whatever, good enough for now. I'll rewrite and move this to an "About" tab later, but want to focus on the blog part.

I'll write posts using markdown [in a separate repo](https://github.com/gnardini/blog-content), it will contain a *posts.json* file and a *posts* folder where the actual posts will live. So, I'll ask Kamara to fetch info about the posts

"Add a getStaticProps function to the index page that fetches posts information from a GitHub repository. The repo is [https://github.com/gnardini/blog-content](https://github.com/gnardini/blog-content) It has a posts.json file that contains a list of objects with a title, a date (in yyyy-mm-dd format) and a content fields. Make getStaticProps return that list and show in the Blog component the list of titles with the date when it was published."

There's more work to be done, but I think this is a good first step. If I give it too much to do at once it's more likely that it will make a mistake.

[GitHub commit](https://github.com/gnardini/blog/commit/88cc460dd39f62dbc0cf81bf567b79bfbe6cac38)

It got it mostly right. The only mistake it made is that it repeated the `Post` interface in both the index page and the Blog component, so I deleted one manually and imported it from the other file. Here's the *getStaticProps* implementation:

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/personal-website/image8.png" alt="getStaticProps">
<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/personal-website/image4.png" alt="Blog Posts">

Time for the next instruction: "Make each post in the Blog component clickable. When clicked it should open a blog/[slug] page. [slug] should be the sluggified title. Add a new page on pages/[slug].ts that has a getStaticProps like the index page. It should filter through the posts list until it finds the right article and use the content field to fetch the full article from the repo. The content field has a path to a markdown file in the blog-content repo. Render the markdown on the page using a library"

I know having the sluggified title in the URL might not be a great idea and might break URLs in the future, but I think it's fine for now, I'll just be careful not to change titles or add redirects if I ever want to change one.

[GitHub commit](https://github.com/gnardini/blog/commit/66a166f77dc90ae4dbacf6e0503377eb2c9b3960)

It worked! Mostly. It did a couple things I didn't know how to do off the top of my head:

* It automatically added a library to render markdown:

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/personal-website/image1.png" alt="Add dependency">

* It wrote the slugify function:

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/personal-website/image5.png" alt="slugify">

* It added a getStaticPaths function I assume to cache all the available pages at build time. I was wondering how this was going to work since I was using getStaticProps, but haven't used Nextjs like this before to be honest so I wasn't sure. Happy I don't have to google it though :) 

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/personal-website/image3.png" alt="getStaticPaths">

It made two mistakes:

* It put an &lt;a> tag inside a &lt;Link> tag, which Next.js doesn't allow. I just removed the inner &lt;a> tag to fix it.
* To fetch the content of each post it was doing `const contentResponse = await axios.get(post.content);` This was trying to read the content from localhost instead of the GitHub repo. I just changed the URL to ```const contentResponse = await axios.get(`https://raw.githubusercontent.com/ gnardini/blog-content/main/${post.content}`);``` and it worked.

It looks awful though. The image is too big and there's no spacing between paragraphs and the title is repeated. They all seem like solvable things though, so I'll just keep going for now and do a manual CSS pass at the end.

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/personal-website/image2.png" alt="Blog Posts">

I'm not happy with the navigation right now. I'll give it a prompt to try to improve it: "Add a Header to all pages. The header should have my picture (you can find it in the Landing component) at the left with my name next to it. To the right of the header add an "About" text that links to the /about page which should just show the Landing component and a Twitter icon that should open [https://twitter.com/gonza_nardini](https://twitter.com/gonza_nardini)"

I'm honestly not sure how I want the page to look yet, but this seems like a good direction.

<img src="https://raw.githubusercontent.com/gnardini/blog-content/main/images/personal-website/image7.png" alt="Header">

[GitHub commit](https://github.com/gnardini/blog/commit/15cbe7ed742c06bef8e362392bde69346938fc6e)

It added the header to the _app.ts file, which is automatically applied to all pages which is nice. It also got the Twitter icon from the `react-icons` library which is part of the template I'm using, I didn't even know it had a Twitter icon there.

One problem it has is that the picture/name at the top left doesn't navigate back to the blog, and I don't want my bio thing to be in the main page anymore since it's in the About one. Next prompt:

"Make the picture and name from the Header component link to / when clicked. Add a Blog item next to the About one that also links to /. Remove the Landing component from the index page"

Nailed it: [GitHub commit](https://github.com/gnardini/blog/commit/f5c021188579d448f02b2c74a9e6be6f420e056a)

Alright, I think feature-wise we're good now, I'll just add some styles manually. I've found that for CSS/UI work GPT doesn't always do the best job. Maybe I'm just prompting it wrong, since it's not my strength either and I don't have a great eye for building beautiful designs.

[...] CSS writing in progress [...]

Done! I used one extra prompt: "Add a Head section with a title and description. The title of each blog page should be the title of the article and for the main one it should be just "Gonza Nardini"" which it got almost right, I just had to move one of the created Head components from the Home page to the _app file so it's used as default. [GitHub commit](https://github.com/gnardini/blog/commit/5e7509c223dcd92a47fcf49804ca85bc2e6fa428)

And finally here's the [GitHub commit](https://github.com/gnardini/blog/commit/56a8e6f3d47ffd56b6c750f08de350f539c38ad4) with the CSS changes. You can check out the final result on https://gnardini.com 

## Final Thoughts

It's been really fun using Kamara to build websites lately and I'll keep building new ones and improving the plugin. There's already a few in the pipeline and I've been using it on existing projects as well (although there are some limitations with big ones). The coding speed that can be achieved is amazing. Hopefully I'll have a version ready to share soon. It's already ready actually, it just has some rough edges, but if you'd like to give it a try reach out to me!

I've also been enjoying writing. I can see myself doing this more often, the whole process is fun and rewarding. What helped was changing my mentality a bit and caring a lot less about whether people will read it or not. I'm writing for myself more than I'm writing for others, which helps a lot.
