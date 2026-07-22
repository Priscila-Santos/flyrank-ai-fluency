# Three Roads: Choosing My Portfolio Stack with AI

## My constraints

**Budget:** Free only.

**My skill level:** I am comfortable with React, TypeScript, HTML, CSS, JavaScript, Tailwind CSS, Spring Boot Java, SQL Language and Git. I have some experience with Next.js and deploying projects on Vercel, but I am still building production experience.

**What my portfolio needs to do:**

* Home page with my professional introduction.
* About section.
* Projects/case studies.
* Certifications.
* Contact page.
* Navigation between pages.
* Responsive design.
* Light and dark mode.
* Display screenshots of my work.
* Link to GitHub repositories.
* Embed live demos when available.
* Present longer case studies explaining my development process.

**How my work must be displayed:**

* Image galleries for project screenshots.
* Embedded live demos.
* GitHub repository links.
* Long-form case study pages describing the problem, solution, AI workflow, and results.

**Does anything need to be dynamic?**

Not yet. My portfolio is primarily static content. A backend is unnecessary at this stage because the contact information can be displayed directly or handled by a third-party form service if needed later.

---

## Option 1 — Static React Site (Vite + React + Tailwind)

**Build**

* React with Vite
* TypeScript
* Tailwind CSS

**Hosting**

* GitHub Pages or Netlify (free)

**Backend**

* No

**Trade-off**

This is the simplest option and very easy to maintain. However, adding features like blog posts, server-side rendering, or better SEO later would require more work or a migration.

---

## Option 2 — Next.js App Router (Chosen)

**Build**

* Next.js
* TypeScript
* Tailwind CSS

**Hosting**

* Vercel (free)

**Backend**

* Not yet

**Trade-off**

This provides routing, excellent performance, SEO benefits, image optimization, and room to grow without requiring a backend today. It introduces a little more complexity than Vite, but it matches my current skills and the technologies I want to showcase.

---

## Option 3 — Full-Stack Next.js

**Build**

* Next.js
* TypeScript
* Tailwind CSS
* API routes or a database

**Hosting**

* Vercel (frontend)
* Free database if needed later

**Backend**

* Yes

**Trade-off**

This is the most powerful option because it supports authentication, CMS features, databases, and dynamic content. However, it also requires maintaining server code, APIs, and database infrastructure that my portfolio does not currently need.

---

## Pressure testing my front-runner

### What breaks if I pick the simplest option?

Nothing immediately breaks because a static React site can display projects, images, repositories, and demos. However, I would lose built-in SEO improvements, image optimization, and an easier path for future expansion.

### What do I maintain if I pick the most powerful option?

I would need to maintain server-side code, API routes, deployments, and potentially a database. That would increase complexity without providing clear benefits for a portfolio website.

### Can I finish in two weeks?

Yes. Next.js with Tailwind is realistic because I already have experience using both technologies and have already built part of my portfolio with this stack.

### Does it display my work the way it needs to?

Yes. It supports image galleries, embedded demos, GitHub links, and detailed case study pages while remaining responsive and performant.

---

## My decision

I chose **Next.js with TypeScript and Tailwind CSS deployed on Vercel**.

I considered a simpler Vite + React application because it would be easier to build and maintain, but Next.js provides better routing, SEO, and long-term flexibility while still remaining free.

I also considered building a full-stack version with API routes and a database, but my portfolio does not currently require dynamic features. Adding backend functionality now would increase maintenance without solving a real problem.

I believe this is the right balance because **I can maintain this**, it is completely free to host, and **it shows my work well** through project pages, screenshots, embedded demos, GitHub repositories, and detailed case studies. If I need dynamic functionality in the future, I can add it without rebuilding the project.
