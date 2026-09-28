# GitHub Foundations Certification — Study Guide

> Translated from the Japanese original: `github-foundations-study-guide.md`

> Notes organized for studying the GitHub Foundations certification. Based on content originally compiled in a personal reference wiki (dotnetdevelopmentinfrastructure.osscons.jp), this systematically organizes GitHub's actual product features (Issues / Pull Requests / Actions / Projects / security administration, etc.). Since this is positioned as **a feature reference for the GitHub product itself, not a "growth-hacking" guide for things like gaining followers**, it is maintained as a separate file from `github-strategy-guide.pptx` (the growth-hacking guide).

## Reference Links

- GitHub Foundations - Study guide PDF / training / hands-on labs / MS Learn Collections (official training materials)
- LinkedIn Learning: Prepare for the GitHub Foundations Certification
- GitHub Foundations practice exam
- Hands-on materials: https://github.com/alterbooth/hol-github-foundations

---

## Table of Contents

- Common Topics (Settings, Templates, Entity Overview)
- Domain 1: Git and GitHub
- Domain 2: Repositories
- Domain 3: Collaboration Features
- Domain 4: Modern Development
- Domain 5: Project Management
- Domain 6: Privacy, Security, and Administration
- Domain 7: Benefits of the GitHub Community

---

## Common Topics

### Settings
Settings can be defined separately for each entity (Personal Account, Repository, Organization, Enterprise).

### Template
Templates can be defined for entities: Repository, Issue, Pull Request, GitHub Actions Workflow.

- **Simple ones**: Markdown format (`*.md`). Intended for providing simple guidelines or example descriptions.
- **Complex ones**: YAML format (`*.yml`). Allows more complex definitions.
- **Placement**: Placed in `.github` → root → `docs` directory, in that order. Where (which folder) multiple files are placed depends on the entity and document file.

### Entity Overview
- Entities that have boards or lists (Repository, Issue, Discussion, Project) can be pinned to display at the top.
- Pull Requests have no pinning feature, so labels are used instead.
- Filters exist in listings for filtering.

---

## Domain 1: Git and GitHub

### Git and GitHub Basics
- Definitions of version control and distributed version control
- The difference between Git and GitHub (Git is a VCS; GitHub is Git hosting + collaboration features)
- GitHub repositories, commits, branches, remotes, the GitHub flow

**Feature hierarchy** (the various features that build on top of Git):
| Category | Features |
|---|---|
| Code management & collaboration | Git repositories, Issues, Pull Requests, Projects, Discussions |
| Operations support & development environment | Actions, Codespaces, Copilot |
| Security | Dependabot, Code scanning, Secret scanning |
| Administration | Organization, Enterprise |

### GitHub Entities
- **Account types**: Personal / Organization / Enterprise
- **Pricing plans**:

| Account | Personal | Organization | Enterprise |
|---|---|---|---|
| Free plan | Free | Free | — |
| Paid plan | Pro | Team | Enterprise |

- GitHub Enterprise offers different deployment options
- User profile features: metadata, achievements, profile README, repositories, pinned repositories, stars, etc.

### GitHub Markdown
- Used in comments on Issues, Pull requests, etc.
- Basic formatting syntax (headings, links, task lists, etc.)
- Syntax insertion via the text formatting toolbar and slash commands

### GitHub Desktop
- `github.com` = the Git hosting service; GitHub Desktop = the client tool
- GitHub Desktop simplifies the development workflow. All git operations can be performed on a cloned repository

### GitHub Mobile
- Quick access to Issue/Pull request dashboards
- Approve Pull requests while on the go
- Mobile-optimized browsing and collaboration, search, notification management

---

## Domain 2: Repositories

### Repository Documentation Files
Placement: `.github` → root → `docs` directory (display priority follows this order)

| File | Role |
|---|---|
| `README.md` | Project overview, usage, installation instructions, sample code, license information |
| `LICENSE.md` | Text of the software license (MIT, Apache, GPL, etc.) |
| `CODEOWNERS.md` | Defines the person(s) responsible (code owners) for specific files/directories. Used for automatic PR review assignment |
| `CONTRIBUTING.md` | How to contribute to the project (PR rules, code style, bug reporting procedure) |
| `CODE_OF_CONDUCT.md` | The project's code of conduct |
| `SECURITY.md` | How to report vulnerabilities, security disclosure policy |
| `SUPPORT.md` | Contact methods, FAQ, forum/Issues usage policy |
| `FUNDING.yml` | Configuration for funding methods such as GitHub Sponsors |
| `CITATION.cff` | How to cite the project for academic papers (Citation File Format) |

### Basic Repository Navigation
Code / Issues / Pull requests / Actions / Projects / Wiki / Security / Insights / Settings (General → including Template Repository settings) / Branches / Commits (history) / clone (fetching and expanding the code)

### How to Create a New Repository
Create a new repository → create a new branch → add files → view repository insights → favorite with a star

**Feature preview** (examples of experimental features that can be enabled from the profile icon): Colorblind themes, Command Palette, Copilot Workspace for Pull Requests, Personal Instructions, New Commit Details Page, Rich Jupyter Notebook Diffs, New Issues Experience, New merge experience, Enhanced Repos Insights Views, Slash Commands

### Template Repositories
Marking a repository as a template makes it possible to create new repositories based on it.

---

## Domain 3: Collaboration Features

### Differences Between Issue / Pull Request / Discussion
| Feature | Purpose |
|---|---|
| Issue | Registering and managing simple tasks. Can also support complex task management in combination with Pull requests/Projects |
| Pull Request | Created from a diff between Git branches, used to review the changes |
| Discussion | Discussion, Q&A, gathering ideas/feedback. Discussion/investigation before something becomes an Issue |

### Issues
- Creating an Issue, creating a Branch from an Issue
- Search and filtering (add filters via dropdowns)
- Management items: Assignees, Label, Milestone, Project, Development (Branch/PullRequest linkage), pinning, linking via `#`, marking as a duplicate (Duplicate of #xx), close/reopen/transfer/delete
- **Issue Template** (placed under `.github/ISSUE_TEMPLATE/`): two types — Markdown format (freely editable, no constraints) and YAML-based Issue Forms (structured, required fields can be set, input field types can be specified)

### Pull Requests
- Created by specifying a base branch and a compare branch. Unlike Issues, Reviewers can be specified
- **Status**: Draft (work in progress) / Open (not merged, not closed) / Closed (ended without merging) / Merged (merged)
- Activity links: references to other PRs, references to Issues (with linked closing keywords), links to comments/commits/lines of code
- **Navigation tabs**: Conversation (discussion/review status) / Commits (list of commits) / Checks (CI/CD status) / Files changed (diff/code review)
- **Review**: a single comment (Add single comment) or a review with multiple comments (Start a review → Finish your review). Suggested changes are written using \`\`\`suggestion\`\`\` blocks and can be applied via commit suggestion
- Review outcomes: Comment (comment only) / Approve (approval) / Request changes (change requested)
- It's a good idea to specify default reviewers via the CODEOWNERS file

### Discussions
- A place for conversation, questions, and information sharing that is not tied to code (became generally available in August 2021)
- Off by default. Enabled from the repository's Settings
- Difference from Issues: Issues are meant for tracking concrete work items, while Discussions are meant for free-form discussion not bound to a particular format, open to the whole community
- Initial categories: General, Announcements, Ideas, Q&A, Show and tell
- Comments can be marked as the answer, a Discussion can be converted to an Issue (auto-linked), and pinning is supported

### Notifications
- Sources: Repository, Issue & Pull Request, GitHub Actions, Dependabot
- Subscription management, Ignored repositories
- The email notification destination can be changed per entity

### GitHub Gist
- A simple way to share code snippets. A Gist is effectively a Git repository, and can be created, forked, and cloned
- Can be set to Public or Secret. **A Public Gist cannot be changed to Secret.** A Secret Gist won't show up in GitHub search, but it is still directly accessible via its URL

### GitHub Wiki and Pages
- **Wiki**: Used to create documentation for a repository. Editable by users with write access
- **Pages**: Static site hosting. A publishing destination for the output of documentation tools. A build process can optionally be added

---

## Domain 4: Modern Development

### GitHub Actions
- Supports automation triggers for 20+ project events, enabling automation of virtually any API call, not just CI/CD
- YAML-based configuration; 17,000+ community-built open source actions

```yaml
name: CI Workflow

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main
  schedule:
    - cron: '0 12 * * 1'  # Runs every Monday at 12:00 UTC

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Check out repository
        uses: actions/checkout@v4
      - name: Set up Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '20'
      - name: Install dependencies
        run: npm install
      - name: Run tests
        run: npm test
```

### GitHub Copilot
- An AI pair programmer that reads context from comments and code to suggest the next input or an entire function
- Can generate code from implementation logic written as comments, and can also suggest test code

### GitHub Codespaces
- A development environment that runs entirely in the browser (write, build, test, debug, deploy)
- Shortens development environment setup time. Environments can be standardized using a dotfiles repository or VS Code extensions
- Supports Deep Links (copying an environment), Live Share (co-editing), Actions integration (testing), secret management, and Remote-SSH/Containers connections
- Lifecycle: Creating (VM billing) → Rebuilding (VM billing) → Stopping (storage billing) → Deleting

**github.dev vs Codespaces**:
| Item | github.dev | Codespaces |
|---|---|---|
| Cost | Free | Free tier available for personal accounts |
| Startup | Ready to use immediately | VM allocation + container configuration via devcontainer.json |
| Compute | Editor only | Debuggable on a VM |
| Terminal | None | Operable just like locally |
| Extensions | Only a subset that can run on the web | Most of the VSCode Marketplace is available |

---

## Domain 5: Project Management

### GitHub Projects
- Import Issues/Pull requests for task management. There are three types of projects: repository, organization, and personal
- **Three layout types**: Table / Board (Kanban) / Roadmap (Gantt chart)
- Flexible field configuration (text, number, single-select, date, and iteration type for sprint management)
- Simple workflows (GitHub Actions integration): fields are automatically updated when triggered by events such as Item added / reopened / closed, Code changes requested, Code review approved, Pull request merged, etc.
- The legacy Projects (classic) was revamped in 2022, and **service ended in August 2024** (it did not support the GraphQL API and had limited customizability)
- Other features: access management independent of the Repository, converting Draft Issues into Issues, visualization of Labels/Milestones, insights (chart graphs), templates for projects/replies

---

## Domain 6: Privacy, Security, and Administration

### Authentication and Security
- **2FA (two-factor authentication)**: Protection via TOTP apps, mobile/desktop devices, or text messages
- **RBAC (Role-Based Access Control)**: Roles define target entities (Enterprise/Organization/Team/Repository) and operations, and are assigned to users. Roles are assigned at the Enterprise/Organization/Team level, while permissions are assigned at the Repository level
- **EMU (Enterprise Managed Users)**: GHEC accounts owned, created, managed, and audited by enterprise administrators. Provisioning/deprovisioning can be automated through IDP integration

### GitHub Administration

**Repository permission levels**:
| Permission | Description | Intended Role |
|---|---|---|
| Read | Read code/Actions, comment on Issues/PRs/Discussions | Non-code contributors |
| Triage | Read access plus Label/assignment management (no write access) | Managing contributors |
| Write | All write access except Repository settings | Code contributors |
| Maintain | Repository administration (excluding deletion and security-related operations) | Project managers |
| Admin | Full administrative access to all features and settings | Overall repository administrators |

**Repository visibility**:
| Visibility | Description |
|---|---|
| Public | Viewable by anyone in the world. Suited for OSS projects |
| Internal | Can only be created within an Organization owned by an Enterprise. Accessible to all members belonging to that same Enterprise (suited for inner source) |
| Private | Accessible only to explicitly added users/Teams |

**Branch protection**: Added via Settings → Code and automation → Rules → Rulesets → New ruleset. A bypass list controls whether administrators can bypass the restrictions. Target branches can also be specified via patterns such as `release-*`. Integrates with CODEOWNERS

**Security features**: Security policy, Security advisories, Private vulnerability reporting, Dependabot alerts, Code scanning alerts, Secret scanning alerts

**Insights**: Pulse, Contributors, Community, Community Standards, Traffic, Commits, Code frequency, Dependency graph, Network, Forks, Actions Usage/Performance Metrics

**People/Roles (Organization)**:
| Role | Description |
|---|---|
| Owner | Full control of the organization + adding/removing users. Recommended to designate two or more |
| Member | Can create/manage repositories and teams |
| Moderator | Can block/unblock collaborators, restrict interactions, hide comments |
| Billing manager | Can view/manage billing information |
| Security managers | Can manage repository access permissions and security alerts |
| Outside collaborator | Has access to one or more Organization repositories |

Team roles: Member (equivalent to an Organization member) / Maintainer (can also perform team maintenance operations). Teams can be subdivided and organized hierarchically.

---

## Domain 7: Benefits of the GitHub Community

### The Open Source Community
- The definition of open source and its benefits
- How to follow people/Organizations (receiving notifications, discovering projects within the community)
- **GitHub Sponsors**: A feature for financial support
- **GitHub Marketplace**: A marketplace for development tools (categories such as Code quality, Code review, CI, Monitoring, Project management, etc.)
- Bringing the benefits of open source in-house (inner source): strengthening collaboration, breaking down silos, improving developer satisfaction

**Elements that make a repository easier to discover and fork**:
- Setting Topics
- A properly structured README
- Having files such as CONTRIBUTING.md in place
- Appropriate label configuration
- Providing Issue/Pull request templates

---

*This note was compiled for the purpose of studying for the GitHub Foundations certification, organizing content from a personal study wiki as a feature reference for the GitHub product. It does not cover operational know-how such as gaining followers, which is managed separately in `github-strategy-guide.pptx` (the GitHub growth-hacking guide).*
