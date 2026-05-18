# Reflection — Open-Source Collaboration and Peer Review
## Assignment 14 | RPL System

---

### Introduction

Preparing the RPL System for open-source collaboration was a fundamentally different challenge from the technical work of Assignments 9 through 13. Building the domain model, service layer, and CI/CD pipeline required precision and logic. Preparing the repository for *other people* required empathy — the ability to stand in the shoes of someone encountering the codebase for the first time and ask: "Would I know what to do here?"

This reflection covers what I improved based on peer feedback, the challenges I faced in designing for onboarding, and the broader lessons I took from simulating open-source collaboration.

---

### How I Improved the Repository Based on Peer Feedback

The most consistent piece of feedback I received from classmates was that while the code and tests were strong, the repository initially felt intimidating to newcomers. The project had grown across five assignments into a sophisticated system with over 275 tests, multiple layered abstractions, and business rules numbered BR-001 through BR-010. For someone joining mid-project, this felt like being handed an instruction manual written in a language they were still learning.

The first improvement I made was rewriting the `CONTRIBUTING.md` from a short checklist into a genuine onboarding guide. I added a full local setup walkthrough including virtual environment creation, dependency installation, and how to verify everything works by running the test suite. I also added a "First-Timer Tips" section — short, direct advice like "Small PRs are better" and "Write the test first" — because I remembered how paralysed I felt the first time I tried to contribute to an open-source project without knowing where to start.

The second major improvement was the issue labelling strategy. Peer feedback revealed that people were willing to contribute but didn't know which issues were safe to attempt. Creating `good-first-issue` tags with detailed, scoped descriptions — including the exact files to change and how many lines of code the change would likely require — dramatically lowered the barrier. Issues #42 through #46 are each small, self-contained tasks where a contributor cannot accidentally break the core business logic.

I also added a `ROADMAP.md` after peers noted the project felt "finished" with no clear direction for where it could go next. The roadmap turned what felt like a static assignment submission into a living project with ambition. Several classmates commented that they actually wanted to fork the repository and implement the PostgreSQL backend or the Docker Compose setup after reading the roadmap — which was exactly the intended effect.

---

### Challenges in Onboarding Contributors

The biggest challenge was calibrating the level of abstraction in the setup instructions. The RPL System uses a layered architecture — domain model, repository layer, service layer, REST API — that makes it clean to work in, but confusing to understand at a glance. A new contributor looking at `services/application_service.py` would see `RPLApplicationService` receiving four injected repositories through its constructor and might not immediately understand why, or how to trace a request from the API endpoint through to the repository.

I addressed this partly through the `CONTRIBUTING.md` project structure diagram and partly by writing inline comments in the service classes explaining the dependency injection pattern. But there is an irreducible complexity to a well-architected system: you cannot make something both sophisticated and trivially simple. The best I could do was provide multiple entry points — the README for a quick overview, the Swagger UI for API exploration without reading code, and the `good-first-issue` labels for targeted contribution without needing to understand the whole system.

A second challenge was writing issues that were specific enough to be actionable but not so prescriptive that they removed the contributor's autonomy. If an issue describes every line of code to write, it is more like a homework problem than a collaborative task. I learned to describe the *what* and *why* clearly, give acceptance criteria, and leave the *how* open — trusting the contributor to bring their own approach.

---

### Lessons Learned About Open-Source Collaboration

The most important lesson was that **documentation is a first-class feature, not an afterthought**. Throughout Assignments 9 to 13, documentation was something I produced at the end — the README, CHANGELOG, and docstrings were written after the code. In an open-source context, this order is backwards. A repository without clear documentation is not open-source in any meaningful sense; it is just public code. Real openness requires writing the `CONTRIBUTING.md` before you expect contributions, and writing issue descriptions clearly enough that someone who has never seen the codebase can pick one up and succeed.

A second lesson was about the relationship between code quality and community. The CI/CD pipeline from Assignment 13 turned out to be one of the most community-friendly things in the repository. When a potential contributor forks the repo and opens a PR, the automated tests immediately tell them whether their changes work — without waiting for a maintainer. This builds trust and reduces friction. A contributor who sees their PR go green knows they did the right thing. A contributor who sees it go red has an immediate, specific signal about what to fix. Automated feedback at scale is what makes open-source communities function.

Finally, the peer review process taught me that receiving feedback gracefully is a skill. The first instinct when someone suggests your code could be clearer is to defend your design decisions. The right response is to ask: "If this person — who is intelligent and motivated — didn't understand this, how many others won't?" Every piece of confusion is a data point. The best open-source maintainers treat their contributors' confusion as a bug to fix, not a failing to excuse.

---

### Conclusion

Preparing the RPL System for open-source collaboration transformed it from a technical exercise into something more like a real project. The work of writing `CONTRIBUTING.md`, labelling issues, and maintaining a roadmap forced me to think about the system from the outside in — as something that exists not just to satisfy assignment criteria but to be useful, understandable, and improvable by others. That shift in perspective is, I think, one of the most valuable things a software development course can produce.

---

*Word count: approximately 900 words*
