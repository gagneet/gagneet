<a href="http://gagneet.com/" target="_blank" rel="noreferrer"><img src="https://user-images.githubusercontent.com/203433/139530832-4c172386-ec46-4ccd-be70-f5e5056d3405.jpg" alt="gagneet banner"></a>

## Hi there 👋 - I'm Gagneet Singh - Technical Product Manager · Principal Engineer · Full-Stack Developer

Let's Start with the Tech Blog & [Live Long and Prosper](http://gagneet.com/)

I'm a **Technical Product Manager, Principal Engineer and Full-Stack Developer** with 20+ years across **Microsoft, Yahoo!, Macromedia/Adobe and Oracle**. I started in quality, performance and automation engineering, and today I design, build, test, secure and self-host my own production platforms. I'm an Automation, Performance and Security testing expert 💻, Love publishing pics I took 📸, and an electronic hobbyist 🎨!

---

## 🚀 My Philosophy & Approach

I believe in empowering teams through comprehensive automation strategies that span from infrastructure provisioning to security testing. My approach combines deep technical expertise in test automation with modern DevOps practices, focusing on creating resilient, scalable systems that enable rapid, secure deployments. I love the entire process of developing and creating innovative ideas to be lazy. Having worked across enterprise environments at Microsoft, Yahoo!, Macromedia/Adobe, and Oracle, I bring a unique perspective on scaling automation across diverse technology stacks.

---

## ✨ Guiding Principles

- **"Automation Over Repetition"**: If a process can be automated, it should be. Manual work is technical debt waiting to happen.
- **"Security by Design, Not by Chance"**: Security considerations must be integrated from architecture through deployment, not retrofitted later.
- **"Fail Fast, Learn Faster"**: Embrace rapid feedback loops through comprehensive monitoring and observability to catch issues early when they're cheapest to fix.
- **"People First, Tools Second"**: The best technology stack means nothing without empowered engineering teams who understand the why behind the what.

---

## 🛠️ Live Products

### 🏢 StrataOS: Multi-Tenant Strata Management ([East Gate Residences](https://eastgateresidences.com.au))
- **What:** SaaS platform for strata schemes under NSW/ACT law. It covers a double-entry ledger, levy runs and arrears, AI OCR for supplier invoices, work orders, AGM voting, compliance registers and a resident portal.
- **Technologies:** Next.js 16, React 19, FastAPI, PostgreSQL (row-level security), MongoDB, Redis/ARQ, Temporal, k6, Playwright
- **Engineering:** 14,000+ backend tests. Every building is an isolated tenant. Money is stored as integer cents, and records are soft-archived for 7 years. The MongoDB → PostgreSQL move uses a shadow-read cutover checked against live data.

### 🧵 [Hamees Attire](https://hamees.gagneet.com): Tailoring Inventory & Order Management
- **What:** Automatic fabric reservation, a production workflow from cutting to delivery, GST invoicing, customer measurements, role-based dashboards and WhatsApp notifications. The public site runs in four languages.
- **Technologies:** Next.js 16, TypeScript, Prisma 7, PostgreSQL 16, NextAuth, PM2, Cloudflare Tunnel
- **Engineering:** 900+ Vitest tests. Order-tracking links are signed and expire after 30 minutes. Enquiry forms are rate-limited and use honeypots. The marketing site has no database access.

### 📈 [Australian Retirement Calculator](https://retirement.gagneet.com)
- **What:** Privacy-first planner that runs entirely in the browser. It models super (SG caps, Division 293), the Age Pension, investment property and healthcare. A reverse planner uses bisection solvers across 11 levers.
- **Engineering:** Monte Carlo runs draw rates independently for each year and report median, p10 and p90 outcomes. Results export to PDF and XLSX. 2,500+ Jest tests.

### 🏠 Home Expenses, [Mortgage](https://mortgage.gagneet.com) & [Investment Property](https://property.gagneet.com) Calculators
- **Home Expenses:** Imports AMEX, CBA, ING and uBank statement PDFs through a FastAPI parser. Blocks duplicate uploads by SHA-256 hash, detects transfers, refunds and subscriptions, and reports budgets and net worth. Validated against 10,000+ real transactions.
- **Mortgage Calculator:** LVR and LMI, stamp duty for NSW/VIC/QLD, offset accounts, extra repayments and amortisation charts.
- **Property Investment Calculator:** An Excel model rebuilt as a Next.js and FastAPI app, with amortisation schedules that handle rate changes, plus cash flow and tax deductions.

### 🎓 [SkillSapien](https://skillsapien.com): Co-founder & CTO
AI career platform with job matching, CV parsing, courses and gamified skill tracking. Business-methods patent filed for its skill-matching system.

### 🏥 Healthcare Platform
Next.js patient management and scheduling app, deployed with Terraform/Azure IaC, Docker, Kubernetes manifests, blue/green pipelines and automated SSL.

---

## 🧪 Testing, Security & Performance

- **Testing:** 17,000+ automated tests across my products (pytest, Jest, Vitest, Playwright). Tenant-isolation suites, CI guardrails against divergent financial calculations, and reconciliation against production data.
- **Security:** PostgreSQL RLS, TOTP MFA, CSRF protection, rate limiting, signed expiring links, dependency audits and security scans in CI, audit trails and formula-injection guards.
- **Performance:** k6 load benchmarks with Grafana dashboards, background job queues, on-demand computation, and tuning of self-hosted nginx, PM2 and systemd services.

---

## 🏢 Enterprise Experience

- **Microsoft:** Enterprise automation. Test and release automation for enterprise products.
- **Yahoo!:** Performance engineering. Load, stress and capacity testing for high-traffic web properties.
- **Macromedia / Adobe:** Quality engineering. Test strategy, automation and release sign-off for creative and web tooling.
- **Oracle:** DevOps leadership. CI/CD, infrastructure automation and release engineering.

---

## 🧰 My DevOps & Test Automation Toolbox

### ☁️ Cloud Platforms & Infrastructure
![Azure](https://img.shields.io/badge/azure-%230072C6.svg?style=for-the-badge&logo=microsoftazure&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Terraform](https://img.shields.io/badge/terraform-%235835CC.svg?style=for-the-badge&logo=terraform&logoColor=white)

### 🧪 Test Automation & Quality Engineering
![Cypress](https://img.shields.io/badge/Cypress-17202C?style=for-the-badge&logo=cypress&logoColor=white)
![Playwright](https://img.shields.io/badge/Playwright-2EAD33?style=for-the-badge&logo=playwright&logoColor=white)
![Selenium](https://img.shields.io/badge/Selenium-43B02A?style=for-the-badge&logo=selenium&logoColor=white)

### 🐳 Containerization & Orchestration
![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)

### 🔄 CI/CD & Automation
![GitHub Actions](https://img.shields.io/badge/github%20actions-%232671E5.svg?style=for-the-badge&logo=githubactions&logoColor=white)
![Jenkins](https://img.shields.io/badge/jenkins-%232C5263.svg?style=for-the-badge&logo=jenkins&logoColor=white)
![Azure DevOps](https://img.shields.io/badge/Azure%20DevOps-0078D7?style=for-the-badge&logo=azure-devops&logoColor=white)

### 💻 Programming Languages & Frameworks
![JavaScript](https://img.shields.io/badge/javascript-%23323330.svg?style=for-the-badge&logo=javascript&logoColor=%23F7DF1E)
![NodeJS](https://img.shields.io/badge/node.js-6DA55F?style=for-the-badge&logo=node.js&logoColor=white)
![React](https://img.shields.io/badge/react-%2320232a.svg?style=for-the-badge&logo=react&logoColor=%2361DAFB)
![Python](https://img.shields.io/badge/python-3670A0?style=for-the-badge&logo=python&logoColor=ffdd54)

### 🛡️ Security & Performance Testing
![OWASP](https://img.shields.io/badge/OWASP-000000?style=for-the-badge&logo=owasp&logoColor=white)
![JMeter](https://img.shields.io/badge/Apache%20JMeter-D22128?style=for-the-badge&logo=apache-jmeter&logoColor=white)

### 🗄️ Databases & Storage
![MySQL](https://img.shields.io/badge/mysql-%2300f.svg?style=for-the-badge&logo=mysql&logoColor=white)
![MongoDB](https://img.shields.io/badge/MongoDB-%234ea94b.svg?style=for-the-badge&logo=mongodb&logoColor=white)
![Redis](https://img.shields.io/badge/redis-%23DD0031.svg?style=for-the-badge&logo=redis&logoColor=white)

### 🔧 Version Control & Collaboration
![Git](https://img.shields.io/badge/git-%23F05033.svg?style=for-the-badge&logo=git&logoColor=white)
![GitHub](https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white)

---

## 🎯 What I'm Currently Focusing On

🏢 **Scaling StrataOS** to more strata schemes and finishing the PostgreSQL cutover.
🔧 **Optimizing Infrastructure as Code** practices with Terraform for healthcare and enterprise environments.
👥 **Mentoring teams** in modern test automation strategies using Cypress and Playwright.
🛡️ **Advancing DevSecOps** practices with integrated security testing throughout the development lifecycle.
📱 **Expanding mobile test automation** capabilities with React Native applications.

---

## 🌱 Continuous Learning

📚 **Deepening expertise in advanced cloud security** patterns and compliance frameworks (HIPAA, SOC2).
🎓 **Focusing on leadership development** for cross-functional team management and enterprise transformation.
☁️ **Developing multi-cloud strategies** for enterprise-grade applications.
🤖 **Exploring AI-powered testing** methodologies and automation enhancement.

---

## 🔬 Currently Exploring & Excited By

- **AI-Assisted Code Generation & Architecture Design**: Leveraging large language models to accelerate development workflows and architectural decision-making, particularly for complex enterprise applications.
- **GitOps for Enterprise Infrastructure**: Implementing declarative, Git-driven infrastructure and application management that meets enterprise compliance requirements while maintaining rapid deployment capabilities.

---

## 🌟 Professional Highlights

🏢 **Enterprise Experience:** Microsoft Corporation, Yahoo!, Macromedia/Adobe, Oracle.
🌏 **Global Leadership:** Tech lead positions across US, Australia, Switzerland, and India.
🚀 **Entrepreneurship:** Co-founder & CTO at Skillsapien.com.
🎓 **Education:** UNSW Business School background.
📄 **Innovation:** Business methods patent filing for unique skill matching systems.

---

## 📝 Recent Blog Posts

<!-- BLOG-POST-LIST:START -->
- [Level Up Your AWS Deployments with Terraform and IaC: A Deep Dive into a Health Startup's Solution](https://thoughtsatwork.wordpress.com)
- [Building Resilient Healthcare Infrastructure with Azure and Terraform](#blog-resilient-healthcare-infra)
- [Modern Test Automation Strategies: From Cypress to Playwright](#blog-modern-test-automation)
- [DevSecOps in Healthcare: Ensuring HIPAA Compliance Through Automation](#blog-devsecops-hipaa)
<!-- BLOG-POST-LIST:END -->

---

## 📊 GitHub Stats & Activity

<div align="center">

![Metrics](https://metrics.lecoq.io/gagneet?template=classic&base=header%2C%20activity%2C%20community%2C%20repositories%2C%20metadata&base.indepth=false&base.hireable=false&base.skip=false&config.timezone=Australia%2FSydney)

![GitHub Streak](https://github-readme-streak-stats.herokuapp.com/?user=gagneet&theme=radical&hide_border=true)

</div>

<!--
The standard GitHub contributions graph will be displayed by GitHub on the profile page below this README.
The Vercel activity graph was removed to reduce clutter and avoid redundancy.
The GitHub Stats (Main Card) and Top Languages card were removed as metrics.lecoq.io provides a comprehensive overview.
-->
<p align="center">
  <!-- Intentionally left blank or can add a different single, high-impact visual later if desired -->
</p>

</p>

---

## 🤝 Connect with me

<p align="center">
  <a href="https://www.linkedin.com/in/gagneet/"><img src="https://img.shields.io/badge/LinkedIn-%230077B5.svg?style=for-the-badge&logo=linkedin&logoColor=white" alt="LinkedIn"/></a>
  <a href="https://thoughtsatwork.wordpress.com"><img src="https://img.shields.io/badge/WordPress-21759B?style=for-the-badge&logo=wordpress&logoColor=white" alt="Blog"/></a>
  <a href="http://gagneet.com/"><img src="https://img.shields.io/badge/Website-FF5722?style=for-the-badge&logo=google-chrome&logoColor=white" alt="Website"/></a>
  <a href="https://twitter.com/gagneet"><img src="https://img.shields.io/badge/Twitter-%231DA1F2.svg?style=for-the-badge&logo=Twitter&logoColor=white" alt="Twitter"/></a>
</p>

If you have any question/feedback, please do not hesitate to reach out to me!

---
<p align="center">
  ⭐️ From <a href="https://github.com/gagneet">gagneet</a>
</p>

<!--
Internal Links for Projects (Can be expanded later into separate files or detailed sections)
<a name="project-healthcare-deep-dive"></a>
### Project Deep Dive: Healthcare Platform Infrastructure (Placeholder)
More details about the architecture, challenges, and solutions...

<a name="project-test-framework-docs"></a>
### Framework Docs: Enterprise Test Automation (Placeholder)
Detailed documentation about the framework components...

<a name="project-test-framework-guide"></a>
### Implementation Guide: Enterprise Test Automation (Placeholder)
Steps and best practices for implementing the framework...

<a name="project-devops-case-studies"></a>
### Case Studies: Multi-Enterprise DevOps Transformation (Placeholder)
Examples of DevOps transformation projects...

<a name="project-devops-methodology"></a>
### Methodology: DevOps Transformation (Placeholder)
Our approach to driving DevOps adoption...

Internal Links for Blog Posts (If not external)
<a name="blog-resilient-healthcare-infra"></a>
### Blog: Resilient Healthcare Infrastructure (Placeholder)
Content for this blog post...

<a name="blog-modern-test-automation"></a>
### Blog: Modern Test Automation Strategies (Placeholder)
Content for this blog post...

<a name="blog-devsecops-hipaa"></a>
### Blog: DevSecOps in Healthcare (Placeholder)
Content for this blog post...
-->
