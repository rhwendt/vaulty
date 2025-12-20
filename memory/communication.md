#memory/communication #workflow

# Communication Standards

> [!NOTE] User Configuration
> Check [[config]] for your personal communication preferences:
> - `team_name` & `organization`: Your team and company
> - `slack_workspace` / `discord_server`: Your communication channels
> - `meeting_preference`: Async-first, sync-required, or hybrid
> - `working_hours` & `timezone`: Your typical schedule
> - `focus_time`: When you prefer no interruptions

## Communication Philosophy

- **Be clear and concise** - Respect everyone's time
- **Assume positive intent** - Give benefit of the doubt
- **Over-communicate** - Better too much than too little
- **Document decisions** - Future you will thank you
- **Choose the right channel** - Use appropriate medium

## Communication Channels

### When to Use Each Channel

**Synchronous (Real-time)**

**Face-to-face / Video Call**
- Complex discussions
- Brainstorming sessions
- Sensitive topics
- Team building
- Pair programming

**Chat / Instant Message**
- Quick questions
- Urgent issues
- Real-time collaboration
- Informal communication
- Status updates

**Asynchronous**

**Email**
- Formal communication
- External stakeholders
- Requires action/approval
- Documentation needed
- Non-urgent updates

**Documentation / Wiki**
- Knowledge sharing
- Processes and procedures
- Project information
- Technical specs
- Decisions and rationale

**Issue Tracker / Project Management**
- Bug reports
- Feature requests
- Task tracking
- Project status

**Code Comments / PR Descriptions**
- Code explanation
- Implementation decisions
- Review feedback
- Technical discussion

## Writing Effective Messages

### Message Structure

**For Questions**
```markdown
**Context**: Brief background (1-2 sentences)
**Question**: Clear, specific question
**What I've Tried**: Show you've done research
**Needed By**: Timeline if urgent
```

**For Updates**
```markdown
**Summary**: TL;DR in one sentence
**Details**: Relevant information
**Action Items**: What needs to be done
**Next Steps**: What's happening next
```

**For Decisions**
```markdown
**Decision**: What was decided
**Context**: Why this came up
**Rationale**: Why this decision
**Impact**: Who/what is affected
**Next Steps**: What happens now
```

### Writing Tips

✅ **DO**
- Start with the most important information
- Use bullet points for clarity
- Include relevant links and references
- Specify action items clearly
- Use proper formatting (bold, headers, code blocks)
- Tag relevant people

❌ **DON'T**
- Bury the lead (get to the point)
- Write walls of text
- Use vague language ("soon", "maybe", "probably")
- Assume everyone has context
- Use passive voice excessively
- Send "just checking in" messages without specifics

## Meeting Practices

### Before the Meeting

**Meeting Invitation Should Include**
- Clear objective/purpose
- Agenda with time allocations
- Pre-reading materials
- Expected outcomes
- List of participants (and why each is needed)

**Example Agenda**
```markdown
## Weekly Team Sync
**Date**: 2025-12-20, 10:00-11:00 AM
**Purpose**: Align on sprint progress and blockers

**Agenda**:
1. Sprint progress (15 min)
2. Blockers and dependencies (20 min)
3. Upcoming priorities (15 min)
4. Open discussion (10 min)

**Pre-read**: Sprint board, previous meeting notes

**Expected Outcome**: Clear action items and blocker resolutions
```

### During the Meeting

**Best Practices**
- Start and end on time
- Assign a facilitator and note-taker
- Follow the agenda
- Park off-topic discussions
- Ensure everyone participates
- Document decisions and action items

**Meeting Roles**

**Facilitator**
- Keep discussion on track
- Manage time
- Ensure everyone is heard
- Guide to decisions

**Note-taker**
- Document key points
- Record decisions
- Note action items
- Share notes after

### After the Meeting

**Meeting Notes Template**
```markdown
## [Meeting Title] - YYYY-MM-DD

**Attendees**: [Names]
**Absent**: [Names]

### Summary
One-paragraph summary of outcomes

### Decisions Made
- Decision 1: [What was decided and why]
- Decision 2: [What was decided and why]

### Action Items
- [ ] Task description (@owner, due date)
- [ ] Task description (@owner, due date)

### Discussion Points
- Topic 1: Brief summary of discussion
- Topic 2: Brief summary of discussion

### Parking Lot
- Items to discuss later

### Next Meeting
- Date/time
- Proposed agenda items
```

## Status Updates

### Daily Standups (Written or Verbal)

```markdown
**Yesterday**:
- Completed task X
- Made progress on task Y

**Today**:
- Will finish task Y
- Start task Z

**Blockers**:
- Waiting on API access
- Need design review
```

### Weekly Updates

```markdown
## Week of [Date]

**Completed**:
- ✅ Feature X launched
- ✅ Fixed critical bug Y
- ✅ Documentation updated

**In Progress**:
- 🔄 Feature Z (60% complete)
- 🔄 Refactoring component A

**Next Week**:
- Complete feature Z
- Start on feature W

**Blockers/Risks**:
- Dependency on team B's API
```

## Giving and Receiving Feedback

### Giving Feedback

**SBI Framework** (Situation-Behavior-Impact)
```
Situation: "In yesterday's code review..."
Behavior: "...you suggested a complete rewrite..."
Impact: "...which made me feel like my work wasn't valued."
```

**Feedback Tips**
- Be specific and timely
- Focus on behavior, not personality
- Offer suggestions, not just criticism
- Balance positive and constructive
- Make it actionable

✅ **Good Feedback**
```
"In the PR review (PR #123), I noticed the function names
weren't following our naming convention. Could you update
them to use camelCase? Happy to pair on this if helpful."
```

❌ **Poor Feedback**
```
"Your code is messy and hard to read."
```

### Receiving Feedback

- **Listen actively** - Don't interrupt or get defensive
- **Ask clarifying questions** - Ensure you understand
- **Thank the person** - Feedback is a gift
- **Reflect** - Consider the feedback objectively
- **Take action** - Implement what makes sense

## Incident Communication

### Incident Updates

**Initial Alert**
```markdown
🚨 INCIDENT: [Brief description]
**Status**: Investigating
**Impact**: [Who/what is affected]
**Started**: [Time]
**Next Update**: [Time]
**Incident Lead**: @person
```

**Progress Updates** (Every 15-30 min)
```markdown
**Update [Time]**:
**Status**: [Investigating/Identified/Fixing/Resolved]
**What we know**: [Current understanding]
**What we're doing**: [Actions being taken]
**Next Update**: [Time]
```

**Resolution**
```markdown
✅ RESOLVED: [Brief description]
**Duration**: [Total time]
**Root Cause**: [What happened]
**Fix**: [What was done]
**Follow-up**: [Prevent recurrence]
**Post-mortem**: [Link when available]
```

## Remote Work Communication

### Best Practices

**Be Explicit**
- State assumptions clearly
- Provide context
- Don't rely on non-verbal cues
- Confirm understanding

**Respect Time Zones**
- Schedule meetings considerately
- Use async communication when possible
- Document for those who can't attend
- Rotate meeting times if needed

**Show Presence**
- Update status in chat
- Respond to messages within reasonable time
- Use video when appropriate
- Participate actively

**Build Rapport**
- Have casual conversations
- Celebrate wins
- Share appropriate personal updates
- Use social channels

## Documentation Communication

### Code Comments

**When to Comment**
```python
# ✅ Good - Explains WHY
# Using binary search because dataset is always sorted
# and can be very large (millions of records)
def find_item(sorted_list, target):
    ...

# ❌ Bad - Explains WHAT (obvious from code)
# Loop through the list
for item in items:
    ...
```

### Commit Messages

**Good Commit Message**
```
feat: Add user avatar upload

Allows users to upload profile pictures with automatic
resizing and S3 storage. Implements image validation
to prevent malicious uploads.

Closes #123
```

### PR Descriptions

See [[code-review]] for detailed PR description templates.

## Crisis Communication

### When Things Go Wrong

**Be Transparent**
- Acknowledge the issue quickly
- Provide regular updates
- Don't hide or minimize
- Explain impact clearly

**Take Ownership**
- Accept responsibility
- Focus on fixing, not blaming
- Learn from mistakes
- Share learnings

**Follow Up**
- Conduct post-mortem
- Document lessons learned
- Implement preventive measures
- Share findings

## Communication Checklist

### Before Sending a Message
- [ ] Is this the right channel?
- [ ] Is my message clear and concise?
- [ ] Have I provided necessary context?
- [ ] Have I included relevant links/references?
- [ ] Are action items clear?
- [ ] Have I tagged the right people?
- [ ] Is the tone appropriate?

### For Important Decisions
- [ ] Document the decision
- [ ] Explain the rationale
- [ ] Note who was involved
- [ ] Identify impacts
- [ ] Communicate to affected parties
- [ ] Update relevant documentation

## Related

- [[project-management]] - For project communication
- [[code-review]] - For code review communication
- [[documentation]] - For documentation standards
