---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
mermaid: true
---

I'm creme332, an undergraduate student studying computer science in Mauritius. This blog is a digital diary where I share my experiences and document my thoughts while working on projects. What drives me is the constant evolution and innovation happening every day in this field, and I enjoy exploring new ways to apply my skills.

Feel free to reach out to me at c34560814 [at] gmail [dot] com if you have any queries.

Stay up-to-date with my latest posts by subscribing to my [RSS feed](/feed.xml).

```mermaid
%%{init: { 'logLevel': 'debug', 'theme': 'base', 'gitGraph': {'showBranches': false, 'showCommitLabel': false,'mainBranchName': 'main'}} }%%
      gitGraph
   commit id: "Initial commit"
   commit id: "Setup blog structure"
   commit id: "Wrote About page"
   branch posts
   checkout posts
   commit id: "First blog post"
   commit id: "Second blog post"
   checkout main
   merge posts id: "Merged first set of posts"
   branch redesign
   checkout redesign
   commit id: "New theme added"
   commit id: "Mobile responsiveness"
   checkout main
   merge redesign id: "Deployed blog redesign"
   commit
```