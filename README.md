# claude-skills

A collection of custom skills for Claude's computer use feature.

## What Is a Skill?

A skill is a portable bundle (`.skill` file) that extends Claude with persistent workflows and user-specific data. Technically, it's a zip archive containing a `SKILL.md` file with instructions Claude follows, plus any supporting data files.

When installed, skills appear in `/mnt/skills/user/`. Claude reads the `SKILL.md` frontmatter to determine when to invoke the skill based on user queries.

### Skill Structure

```
{skill-name}/
├── SKILL.md          # Core instructions (required)
├── version.txt       # Semantic version
└── ...               # Skill-specific data and templates
```

### Installation

1. Download the `.skill` file
2. In Claude (claude.ai or desktop app), go to **Settings → Skills**
3. Upload the skill bundle

## Data Persistence

User data lives inside the skill bundle. To preserve changes across reinstalls or share with others, ask Claude to export the bundle. Most skills support two export modes: full data export and blank template export (for sharing without personal data).

## UX Patterns

Some effective skill UX patterns are documented.

### Interview and Improve

```mermaid
flowchart LR
    START(["User Invokes Skill"])
    CHECK{"Initialized?"}
    INIT["Interview"]
    IDENTIFY(["Identify Task"])
    WHAT{"What Task?"}
    
    START --> CHECK
    CHECK -- No --> INIT
    CHECK -- Yes --> IDENTIFY
    INIT --> IDENTIFY
    IDENTIFY --> WHAT
    PUBLISH["Publish"]
    ANOTHER{"Another Task?"}
    UPD_PROC{"Update Process?"}
    UPD_STATE{"Update State?"}
    DONE(["Session End"])
    WHAT -- Task 1 --> TASK_1
    WHAT -- Task 2 --> TASK_2
    
    subgraph TASK_1["Task 1"]
        direction TB
        T1_I["Interview"]
        T1_W["Work"]
        T1_V["Verify"]
        T1_I --> T1_W --> T1_V
    end
    
    subgraph TASK_2["Task 2"]
        direction TB
        T2_I["Interview"]
        T2_W["Work"]
        T2_V["Verify"]
        T2_I --> T2_W --> T2_V
    end
    TASK_1 --> IMPROVE
    TASK_2 --> IMPROVE
    
    subgraph IMPROVE["Continuous Improvement"]
        direction TB
        CI_A["Assess"]
        CI_I["Interview"]
        CI_A --> CI_I
    end
    
    IMPROVE --> PUBLISH
    PUBLISH --> ANOTHER
    ANOTHER -- Yes --> IDENTIFY
    ANOTHER -- No --> UPD_PROC
    UPD_PROC -- Yes --> PROC_UPD
    UPD_PROC -- No --> UPD_STATE
    
    subgraph PROC_UPD["Process Update"]
        PU_D["process_update_detail"]
    end
    
    PROC_UPD --> UPD_STATE
    
    UPD_STATE -- Yes --> STATE_UPD
    UPD_STATE -- No --> DONE
    
    subgraph STATE_UPD["State Update"]
        SU_D["state_update_detail"]
    end
    
    STATE_UPD --> DONE
```
---

## Skills in This Repo

### book-ranking

Book recommendation and ranking for book clubs.

**Capabilities:**
- Rank candidate books against your preferences and club context
- Maintain per-club profiles and reading history
- Learn from feedback when rankings miss the mark

**Data files:**
- `user-profile.md` — Your reading preferences (fiction/nonfiction balance, density tolerance, exhausted territory)
- `heuristics.md` — Accumulated ranking adjustments from calibration sessions
- `book-clubs/{club}/` — Per-club profiles and reading history

**Example queries:**
- "Rank these five books for my book club"
- "Set up a new book club profile"
- "We just finished [book]—log it"

On first use, the skill runs an initialization interview to populate your reading profile.
