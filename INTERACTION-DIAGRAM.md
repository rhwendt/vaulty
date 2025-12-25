# Vaulty Interaction Diagrams

Visual representations of how Vaulty's context memory system works.

## 1. Overall System Flow

```mermaid
flowchart TD
    User["👤 USER<br/>'Write a Python script to process CSV'"]

    subgraph Claude["🤖 CLAUDE"]
        Step1["Step 1: Read .claude.md<br/>(trigger words & workflows)"]
        Step2["Step 2: ⚠️ CHECK CONFIG FIRST! ⚠️<br/>Read config.md for user preferences:<br/>• languages: Python<br/>• indent_size: 4<br/>• repos_directory: ~/code<br/>• test_framework_python: pytest"]
        Step3["Step 3: Identify trigger<br/>'Write' = Developer Agent workflow"]

        Step1 --> Step2
        Step2 --> Step3
    end

    Workflow["⚙️ AGENT WORKFLOW ACTIVATED"]

    subgraph Agents["Agent Pipeline"]
        Dev["Developer Agent"]
        Test["Tester Agent"]
        Audit["Auditor Agent"]
        Git["Git Agent"]

        Dev --> Test --> Audit --> Git
    end

    User --> Claude
    Step3 --> Workflow
    Workflow --> Agents

    style User fill:#0288d1,stroke:#01579b,stroke-width:2px,color:#fff
    style Claude fill:#f57c00,stroke:#e65100,stroke-width:2px,color:#fff
    style Workflow fill:#388e3c,stroke:#1b5e20,stroke-width:2px,color:#fff
    style Agents fill:#7b1fa2,stroke:#4a148c,stroke-width:2px,color:#fff
```

## 2. Detailed Agent Collaboration Workflow

```mermaid
flowchart TD
    UserReq["👤 USER REQUEST<br/>'Write a Python function to validate emails'"]

    subgraph DevAgent["💻 1. DEVELOPER AGENT"]
        DevRead["📖 Read Context:<br/>• config → language: Python, indent: 4<br/>• memory/architecture-design → patterns<br/>• memory/code-review → standards"]
        DevCode["✍️ Write Code:<br/>import re<br/>def validate_email(email: str) -> bool:<br/>    pattern = r'^[\\w\\.-]+@[\\w\\.-]+\\.\\w+$'<br/>    return bool(re.match(pattern, email))"]
        DevDone["✅ Code written"]

        DevRead --> DevCode --> DevDone
    end

    subgraph TestAgent["🧪 2. TESTER AGENT"]
        TestRead["📖 Read Context:<br/>• config → test_framework: pytest<br/>• config → minimum_coverage: 70%<br/>• memory/testing-qa → test standards"]
        TestCode["✍️ Write Tests:<br/>def test_validate_email_valid():<br/>    assert validate_email('user@example.com')<br/>def test_validate_email_invalid():<br/>    assert not validate_email('invalid')"]
        TestRun["▶️ Run tests → All pass, 85% coverage"]
        TestDone["✅ Tests pass"]

        TestRead --> TestCode --> TestRun --> TestDone
    end

    subgraph AuditAgent["🔍 3. AUDITOR AGENT"]
        AuditRead["📖 Read Context:<br/>• config → max_line_length: 100<br/>• config → linter: pylint<br/>• memory/code-review → security checklist"]
        AuditReview["🔎 Review Code:<br/>✓ No security issues<br/>✓ Code quality good<br/>✓ Tests adequate (>70%)<br/>✓ Follows code style (4-space indent)"]
        AuditDone["✅ AUDIT PASSED"]

        AuditRead --> AuditReview --> AuditDone
    end

    subgraph GitAgent["📦 4. GIT AGENT"]
        GitRead["📖 Read Context:<br/>• config → repos_directory: ~/code<br/>• config → default_branch: main<br/>• config → commit_style: conventional<br/>• memory/git-workflow → best practices"]
        GitOps["⚙️ Git Operations:<br/>$ cd ~/code/project<br/>$ git add validate_email.py test_validate_email.py<br/>$ git commit -m 'feat: Add email validation'<br/>$ git push -u origin feature/email-validation"]
        GitDone["✅ COMMITTED & PUSHED"]

        GitRead --> GitOps --> GitDone
    end

    Result["🎉 RESULT TO USER<br/><br/>✅ Feature implemented with tests<br/>✅ Code reviewed and approved<br/>✅ Committed to git<br/><br/>Files:<br/>• validate_email.py (implementation)<br/>• test_validate_email.py (85% coverage)<br/>• Committed to: feature/email-validation"]

    UserReq --> DevAgent
    DevDone --> TestAgent
    TestDone --> AuditAgent
    AuditDone --> GitAgent
    GitDone --> Result

    style DevAgent fill:#1976d2,stroke:#0d47a1,color:#fff,stroke-width:2px
    style TestAgent fill:#8e24aa,stroke:#4a148c,color:#fff,stroke-width:2px
    style AuditAgent fill:#f57c00,stroke:#e65100,color:#fff,stroke-width:2px
    style GitAgent fill:#388e3c,stroke:#1b5e20,color:#fff,stroke-width:2px
    style Result fill:#2e7d32,stroke:#1b5e20,color:#fff,stroke-width:3px
```

## 3. Configuration Flow Through System

```mermaid
flowchart TB
    Config["⚙️ config.md<br/>(User's Personal Settings)<br/><br/>name: 'John Developer'<br/>repos_directory: '~/code'<br/>default_branch: 'main'<br/>languages: [Python, JavaScript]<br/>indent_size: 4<br/>test_framework_python: 'pytest'<br/>minimum_coverage: 80<br/>cloud_provider: 'AWS'<br/>..."]

    Memory["📁 Memory Files"]
    Agents["🤖 Agents"]
    ClaudeMD["📋 .claude.md"]
    Claude["🧠 Claude"]

    Config --> Memory
    Config --> Agents
    Config --> ClaudeMD
    Config --> Claude

    subgraph MemoryDetails["Memory File Behavior"]
        MemRef["References config for standards"]
        MemEx["Example: git-workflow references:<br/>• default_branch<br/>• commit_style<br/>• git_workflow"]
        MemRef --> MemEx
    end

    subgraph AgentDetails["Agent Behavior"]
        AgentRef["References config for behavior"]
        AgentEx["Example: git-agent checks:<br/>• repos_directory<br/>• default_branch<br/>• commit_style"]
        AgentRef --> AgentEx
    end

    subgraph ClaudeDetails[".claude.md Behavior"]
        ClaudeRef["References config as priority #1"]
        ClaudeEx["⚠️ 'CHECK CONFIG FIRST!'"]
        ClaudeRef --> ClaudeEx
    end

    subgraph ClaudeAI["Claude AI Behavior"]
        ClaudeRead["Reads config first"]
        ClaudeUse["Uses user's preferences<br/>throughout interaction"]
        ClaudeRead --> ClaudeUse
    end

    Memory -.-> MemoryDetails
    Agents -.-> AgentDetails
    ClaudeMD -.-> ClaudeDetails
    Claude -.-> ClaudeAI

    style Config fill:#f57f17,stroke:#e65100,stroke-width:4px,color:#fff
    style Memory fill:#0288d1,stroke:#01579b,color:#fff,stroke-width:2px
    style Agents fill:#8e24aa,stroke:#4a148c,color:#fff,stroke-width:2px
    style ClaudeMD fill:#f57c00,stroke:#e65100,color:#fff,stroke-width:2px
    style Claude fill:#388e3c,stroke:#1b5e20,color:#fff,stroke-width:2px
```

## 4. Memory Files & Agent Relationships

```mermaid
graph TB
    subgraph MemoryFiles["📚 MEMORY FILES<br/>(Best Practices)<br/><br/>Each references config for user preferences"]
        GitWF["📄 git-workflow.md"]
        ProjMgmt["📄 project-management.md"]
        TestQA["📄 testing-qa.md"]
        Deploy["📄 deployment.md"]
        Docs["📄 documentation.md"]
        CodeRev["📄 code-review.md"]
        Comm["📄 communication.md"]
        ArchDesign["📄 architecture-design.md"]
    end

    subgraph Agents["🤖 SPECIALIZED AGENTS<br/>(Each checks config first!)"]
        GitAgent["📦 Git Agent"]
        DevAgent["💻 Developer Agent"]
        TestAgent["🧪 Tester Agent"]
        AuditAgent["🔍 Auditor Agent"]
        DeployAgent["🚀 Deployment Agent"]
        PMAgent["📊 Project Mgr Agent"]
        ProjDesignAgent["🎯 Project Designer Agent"]
        DocsAgent["📝 Documentation Agent"]
        DebugAgent["🐛 Debugger Agent"]
        ArchAgent["🏛️ Architect Agent"]
        DesignAgent["🎨 Software Design Agent"]
    end

    GitWF -.->|Referenced by| GitAgent

    ArchDesign -.->|Referenced by| DevAgent
    CodeRev -.->|Referenced by| DevAgent

    TestQA -.->|Referenced by| TestAgent

    CodeRev -.->|Referenced by| AuditAgent
    TestQA -.->|Referenced by| AuditAgent

    Deploy -.->|Referenced by| DeployAgent

    ProjMgmt -.->|Referenced by| PMAgent

    ArchDesign -.->|Referenced by| ProjDesignAgent
    ProjMgmt -.->|Referenced by| ProjDesignAgent

    Docs -.->|Referenced by| DocsAgent

    Comm -.->|Referenced by| DebugAgent

    ArchDesign -.->|Referenced by| ArchAgent
    ProjMgmt -.->|Referenced by| ArchAgent

    ArchDesign -.->|Referenced by| DesignAgent
    CodeRev -.->|Referenced by| DesignAgent

    style MemoryFiles fill:#388e3c,stroke:#1b5e20,color:#fff,stroke-width:2px
    style Agents fill:#1976d2,stroke:#0d47a1,color:#fff,stroke-width:2px
    style GitWF fill:#f9a825,stroke:#f57f17,stroke-width:2px,color:#000
    style ProjMgmt fill:#f9a825,stroke:#f57f17,stroke-width:2px,color:#000
    style TestQA fill:#f9a825,stroke:#f57f17,stroke-width:2px,color:#000
    style Deploy fill:#f9a825,stroke:#f57f17,stroke-width:2px,color:#000
    style Docs fill:#f9a825,stroke:#f57f17,stroke-width:2px,color:#000
    style CodeRev fill:#f9a825,stroke:#f57f17,stroke-width:2px,color:#000
    style Comm fill:#f9a825,stroke:#f57f17,stroke-width:2px,color:#000
    style ArchDesign fill:#f9a825,stroke:#f57f17,stroke-width:2px,color:#000
```

## 5. Quick Example Interactions

### Example A: "Commit these changes"

```mermaid
flowchart TD
    User["👤 User: 'Commit these changes'"]
    Read["🤖 Claude reads .claude.md"]
    Trigger["🎯 Trigger: 'commit' → Git Agent"]

    subgraph GitAgent["📦 Git Agent"]
        Step1["1️⃣ Read config.md<br/>→ repos_directory, default_branch, commit_style"]
        Step2["2️⃣ Read memory/git-workflow.md<br/>→ best practices"]
        Step3["3️⃣ Run: git status, git diff"]
        Step4["4️⃣ Create commit message<br/>(conventional style from config)"]
        Step5["5️⃣ Commit and push"]

        Step1 --> Step2 --> Step3 --> Step4 --> Step5
    end

    Done["✅ Done!"]

    User --> Read --> Trigger --> GitAgent --> Done

    style User fill:#0288d1,stroke:#01579b,color:#fff,stroke-width:2px
    style GitAgent fill:#388e3c,stroke:#1b5e20,color:#fff,stroke-width:2px
    style Done fill:#2e7d32,stroke:#1b5e20,color:#fff,stroke-width:3px
```

### Example B: "Deploy to production"

```mermaid
flowchart TD
    User["👤 User: 'Deploy to production'"]
    Read["🤖 Claude reads .claude.md"]
    Trigger["🎯 Trigger: 'deploy' → Deployment Agent"]

    subgraph DeployAgent["🚀 Deployment Agent"]
        Step1["1️⃣ Read config.md<br/>→ cloud_provider: AWS, environments"]
        Step2["2️⃣ Read memory/deployment.md<br/>→ deployment checklist"]
        Step3["3️⃣ Run pre-deployment checks"]
        Step4["4️⃣ Deploy to AWS<br/>using user's CI/CD pipeline"]
        Step5["5️⃣ Monitor deployment"]

        Step1 --> Step2 --> Step3 --> Step4 --> Step5
    end

    Done["✅ Done!"]

    User --> Read --> Trigger --> DeployAgent --> Done

    style User fill:#0288d1,stroke:#01579b,color:#fff,stroke-width:2px
    style DeployAgent fill:#f57c00,stroke:#e65100,color:#fff,stroke-width:2px
    style Done fill:#2e7d32,stroke:#1b5e20,color:#fff,stroke-width:3px
```

### Example C: "Fix this bug"

```mermaid
flowchart TD
    User["👤 User: 'The login function isn't working'"]
    Read["🤖 Claude reads .claude.md"]
    Trigger["🎯 Trigger: 'bug' → Debugger Agent"]

    subgraph DebugAgent["🐛 Debugger Agent"]
        D1["1️⃣ Read config.md<br/>→ workspace, languages"]
        D2["2️⃣ Investigate in user's<br/>workspace directory"]
        D3["3️⃣ Find root cause"]
        D1 --> D2 --> D3
    end

    Handoff1["🤝 Hands off to Developer Agent"]

    subgraph DevAgent["💻 Developer Agent"]
        Dev1["1️⃣ Read config.md<br/>→ code style preferences"]
        Dev2["2️⃣ Fix the bug"]
        Dev1 --> Dev2
    end

    Handoff2["🤝 Hands off to Tester Agent"]

    subgraph TestAgent["🧪 Tester Agent"]
        T1["1️⃣ Read config.md<br/>→ test framework"]
        T2["2️⃣ Add regression test"]
        T1 --> T2
    end

    Handoff3["🤝 Hands off to Auditor Agent"]

    subgraph AuditAgent["🔍 Auditor Agent"]
        A1["1️⃣ Review fix"]
    end

    Handoff4["🤝 If PASS, hand off to Git Agent"]

    subgraph GitAgent["📦 Git Agent"]
        G1["1️⃣ Read config.md<br/>→ git preferences"]
        G2["2️⃣ Commit the fix"]
        G1 --> G2
    end

    Done["✅ Done!"]

    User --> Read --> Trigger --> DebugAgent
    DebugAgent --> Handoff1 --> DevAgent
    DevAgent --> Handoff2 --> TestAgent
    TestAgent --> Handoff3 --> AuditAgent
    AuditAgent --> Handoff4 --> GitAgent
    GitAgent --> Done

    style User fill:#0288d1,stroke:#01579b,color:#fff,stroke-width:2px
    style DebugAgent fill:#c62828,stroke:#b71c1c,color:#fff,stroke-width:2px
    style DevAgent fill:#1976d2,stroke:#0d47a1,color:#fff,stroke-width:2px
    style TestAgent fill:#8e24aa,stroke:#4a148c,color:#fff,stroke-width:2px
    style AuditAgent fill:#f57c00,stroke:#e65100,color:#fff,stroke-width:2px
    style GitAgent fill:#388e3c,stroke:#1b5e20,color:#fff,stroke-width:2px
    style Done fill:#2e7d32,stroke:#1b5e20,color:#fff,stroke-width:3px
```

### Example D: "Help me design a new project"

```mermaid
flowchart TD
    User["👤 User: 'I want to build a task management API with real-time updates'"]
    Read["🤖 Claude reads .claude.md"]
    Trigger["🎯 Trigger: 'build' + 'new project' → Project Designer Agent"]

    subgraph ProjDesignAgent["🎯 PROJECT DESIGNER AGENT"]
        PD1["1️⃣ Read config.md<br/>→ languages, frameworks, databases, cloud_provider"]
        PD2["2️⃣ Gather requirements<br/>❓ How many users?<br/>❓ Real-time: WebSockets or SSE?<br/>❓ Data model complexity?<br/>❓ MVP vs future features?"]
        PD3["3️⃣ Explore technology options<br/>📊 Language: Node.js vs Go vs Python<br/>📊 Database: PostgreSQL vs MongoDB<br/>📊 Real-time: Socket.io vs native WebSockets"]
        PD4["4️⃣ Design architecture<br/>🏛️ API design (REST + WebSockets)<br/>🏛️ Database schema<br/>🏛️ Authentication strategy"]
        PD5["5️⃣ Create implementation roadmap<br/>📋 Phase 1: Foundation<br/>📋 Phase 2: Core features<br/>📋 Phase 3: Real-time & deploy"]
        PD1 --> PD2 --> PD3 --> PD4 --> PD5
    end

    Handoff1["🤝 Hands off to Architect Agent"]

    subgraph ArchAgent["🏛️ ARCHITECT AGENT"]
        ARCH1["1️⃣ Create ADRs<br/>📝 ADR-001: Technology choice<br/>📝 ADR-002: Database choice<br/>📝 ADR-003: Architecture pattern"]
        ARCH1
    end

    Handoff2["🤝 Hands off to Project Manager Agent"]

    subgraph PMAgent["📊 PROJECT MANAGER AGENT"]
        PM1["1️⃣ Create project structure<br/>📁 projects/task-management-api/<br/>📄 overview.md<br/>📂 tasks/"]
        PM2["2️⃣ Create initial tasks<br/>📝 task-001-setup-repository<br/>📝 task-002-database-schema<br/>📝 task-003-api-endpoints<br/>📝 task-004-websocket-server<br/>📝 task-005-testing"]
        PM1 --> PM2
    end

    Handoff3["🤝 Ready for Developer Agent"]

    Done["✅ Project designed and ready!<br/><br/>📋 Requirements documented<br/>🏛️ Architecture designed<br/>📝 ADRs created<br/>📂 Vaulty project set up<br/>📝 Tasks ready to implement"]

    User --> Read --> Trigger --> ProjDesignAgent
    ProjDesignAgent --> Handoff1 --> ArchAgent
    ArchAgent --> Handoff2 --> PMAgent
    PMAgent --> Handoff3 --> Done

    style User fill:#0288d1,stroke:#01579b,color:#fff,stroke-width:2px
    style ProjDesignAgent fill:#7b1fa2,stroke:#4a148c,color:#fff,stroke-width:2px
    style ArchAgent fill:#1976d2,stroke:#0d47a1,color:#fff,stroke-width:2px
    style PMAgent fill:#8e24aa,stroke:#4a148c,color:#fff,stroke-width:2px
    style Done fill:#2e7d32,stroke:#1b5e20,color:#fff,stroke-width:3px
```

## 6. Complete Feature Development Workflow

This diagram shows the full 10-step agent collaboration workflow for implementing a major feature.

```mermaid
flowchart TD
    User["👤 USER REQUEST<br/>'Implement user authentication system'"]

    subgraph Step1["1️⃣ PROJECT MANAGER AGENT"]
        PM1["📖 Read config.md"]
        PM2["📋 Create/track task in projects/"]
        PM3["✅ Task created"]
        PM1 --> PM2 --> PM3
    end

    subgraph Step2["2️⃣ ARCHITECT AGENT"]
        A1["📖 Read config.md + memory/architecture-design"]
        A2["🏛️ Design high-level architecture<br/>• Database schema<br/>• API endpoints<br/>• Security model"]
        A3["📝 Create ADR (Architecture Decision Record)"]
        A4["✅ Architecture designed"]
        A1 --> A2 --> A3 --> A4
    end

    subgraph Step3["3️⃣ SOFTWARE DESIGN AGENT"]
        SD1["📖 Read config.md + memory/architecture-design"]
        SD2["🎨 Design code structure<br/>• Classes/modules<br/>• Design patterns<br/>• Interfaces"]
        SD3["✅ Design complete"]
        SD1 --> SD2 --> SD3
    end

    subgraph Step4["4️⃣ DEVELOPER AGENT"]
        D1["📖 Read config.md + memory files"]
        D2["✍️ Implement code<br/>Following design & architecture"]
        D3["✅ Code written"]
        D1 --> D2 --> D3
    end

    subgraph Step5["5️⃣ TESTER AGENT"]
        T1["📖 Read config.md + memory/testing-qa"]
        T2["🧪 Write comprehensive tests"]
        T3["▶️ Run tests"]
        T4{"Tests pass?"}
        T5["✅ All tests pass"]
        T1 --> T2 --> T3 --> T4
        T4 -->|Yes| T5
    end

    FailBack1["❌ Return to Developer"]

    subgraph Step6["6️⃣ AUDITOR AGENT"]
        AU1["📖 Read config.md + memory/code-review"]
        AU2["🔍 Review code<br/>• Security check<br/>• Quality check<br/>• Performance check"]
        AU3{"Review pass?"}
        AU4["✅ Audit passed"]
        AU1 --> AU2 --> AU3
        AU3 -->|Yes| AU4
    end

    FailBack2["❌ Return to Developer"]

    subgraph Step7["7️⃣ GIT AGENT"]
        G1["📖 Read config.md + memory/git-workflow"]
        G2["📦 Commit and push<br/>Following git standards"]
        G3["✅ Code committed"]
        G1 --> G2 --> G3
    end

    subgraph Step8["8️⃣ DOCUMENTATION AGENT"]
        DOC1["📖 Read config.md + memory/documentation"]
        DOC2["📝 Update documentation<br/>• README<br/>• API docs<br/>• User guides"]
        DOC3["✅ Docs updated"]
        DOC1 --> DOC2 --> DOC3
    end

    subgraph Step9["9️⃣ DEPLOYMENT AGENT"]
        DEP1["📖 Read config.md + memory/deployment"]
        DEP2["🚀 Deploy to environments<br/>• Staging<br/>• Production (if approved)"]
        DEP3["📊 Monitor deployment"]
        DEP4["✅ Deployed successfully"]
        DEP1 --> DEP2 --> DEP3 --> DEP4
    end

    subgraph Step10["🔟 PROJECT MANAGER AGENT"]
        PM4["📋 Update task status"]
        PM5["✅ Mark task complete"]
        PM4 --> PM5
    end

    Complete["🎉 FEATURE COMPLETE!"]

    User --> Step1
    PM3 --> Step2
    A4 --> Step3
    SD3 --> Step4
    D3 --> Step5
    T4 -->|No| FailBack1 --> Step4
    T5 --> Step6
    AU3 -->|No| FailBack2 --> Step4
    AU4 --> Step7
    G3 --> Step8
    DOC3 --> Step9
    DEP4 --> Step10
    PM5 --> Complete

    style User fill:#0288d1,stroke:#01579b,color:#fff,stroke-width:2px
    style Step1 fill:#8e24aa,stroke:#4a148c,color:#fff,stroke-width:2px
    style Step2 fill:#1976d2,stroke:#0d47a1,color:#fff,stroke-width:2px
    style Step3 fill:#43a047,stroke:#1b5e20,color:#fff,stroke-width:2px
    style Step4 fill:#1976d2,stroke:#0d47a1,color:#fff,stroke-width:2px
    style Step5 fill:#8e24aa,stroke:#4a148c,color:#fff,stroke-width:2px
    style Step6 fill:#f57c00,stroke:#e65100,color:#fff,stroke-width:2px
    style Step7 fill:#388e3c,stroke:#1b5e20,color:#fff,stroke-width:2px
    style Step8 fill:#00838f,stroke:#004d40,color:#fff,stroke-width:2px
    style Step9 fill:#f57c00,stroke:#e65100,color:#fff,stroke-width:2px
    style Step10 fill:#8e24aa,stroke:#4a148c,color:#fff,stroke-width:2px
    style Complete fill:#2e7d32,stroke:#1b5e20,color:#fff,stroke-width:4px
    style FailBack1 fill:#c62828,stroke:#b71c1c,color:#fff,stroke-width:2px
    style FailBack2 fill:#c62828,stroke:#b71c1c,color:#fff,stroke-width:2px
```

**Key Points:**
- **Config-driven**: Every agent checks `config.md` first for personalization
- **Quality gates**: Tests and audits act as checkpoints
- **Failure loops**: Failed tests/audits return to Developer for fixes
- **Complete lifecycle**: From planning to deployment to completion tracking
- **Collaborative**: 10 different specialized agents working together

## Key Takeaways

1. **Config First**: Every operation starts by checking user's config
2. **Memory Files**: Provide best practices for each domain
3. **Agents**: Specialized experts that reference config + memory
4. **Workflows**: Agents collaborate (Developer → Tester → Auditor → Git)
5. **Personalized**: Everything adapts to YOUR tools, style, and workflow

---

*This is a living document. As you use Vaulty, you'll see these patterns in action!*
