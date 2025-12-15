# Ragbot UI/UX Redesign Project Plan

> Location: `ai-knowledge-ragbot/source/project-plans/ragbot-ui-redesign-plan.md`
> Created: 2025-12-14
> Status: Planning

---

## Overview

This document outlines the plan to redesign Ragbot's user interface to better reflect its core value proposition as a RAG-enabled AI assistant. The current sidebar-based Streamlit layout has usability issues and hides RAG functionality in "Advanced Settings" despite "RAG" being in the product name.

## Current State Assessment

### Problems with Current UI

1. **RAG Hidden in Advanced Settings**: RAG is Ragbot's core differentiator but requires users to find and enable it manually
2. **Narrow Sidebar**: Dropdown menus are truncated, model names unreadable
3. **Wasted Space**: Sidebar takes up valuable horizontal space that could be used for chat
4. **No Index Management**: Users can't see index status or manage workspace indexes
5. **Manual Indexing Required**: Users must manually click "Index Workspace" before RAG works

### Current Layout

```
┌────────────┬────────────────────────────────────────┐
│ Sidebar    │                                        │
│ (narrow)   │           Chat Area                    │
│            │                                        │
│ - Workspace│                                        │
│ - Model    │                                        │
│ - Settings │                                        │
│ (truncated)│                                        │
└────────────┴────────────────────────────────────────┘
```

## Design Goals

1. **RAG by Default**: RAG should be enabled automatically, not hidden
2. **Auto-Indexing**: Index should be built automatically when missing
3. **Wider Chat Area**: Maximize space for the primary interaction
4. **Readable Controls**: All settings should be fully visible and accessible
5. **Index Visibility**: Users should see index status at a glance
6. **Compiler Integration**: Ability to recompile from sources when needed

---

## Proposed Design: Top Bar + Modal Settings

### Layout

```
┌─────────────────────────────────────────────────────────────────┐
│ 🤖 Ragbot   [Workspace ▼]  [Claude Sonnet ▼]  [Balanced ▼]  ⚙️ 📚│
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│                                                                 │
│                       Chat Area                                 │
│                       (Full Width)                              │
│                                                                 │
│                                                                 │
│                                                                 │
├─────────────────────────────────────────────────────────────────┤
│ [Message input...]                                      [Send]  │
└─────────────────────────────────────────────────────────────────┘
```

### Top Bar Components

| Element | Description |
|---------|-------------|
| 🤖 Ragbot | Logo/title |
| [Workspace ▼] | Workspace selector dropdown |
| [Model ▼] | Model selector (shows current model) |
| [Creativity ▼] | Temperature preset (Precise/Balanced/Creative) |
| ⚙️ | Opens Settings modal |
| 📚 | Opens Index Management screen |

### Settings Modal (⚙️)

```
┌─────────────────────────────────────────────┐
│ Settings                              [×]   │
├─────────────────────────────────────────────┤
│                                             │
│ Model Configuration                         │
│ ┌─────────────────────────────────────────┐ │
│ │ Provider: [Anthropic ▼]                 │ │
│ │ Model:    [Claude Sonnet 4.5 ▼]         │ │
│ │ Max Tokens: [4096 ▼]                    │ │
│ └─────────────────────────────────────────┘ │
│                                             │
│ RAG Configuration                           │
│ ┌─────────────────────────────────────────┐ │
│ │ [✓] Enable RAG (recommended)            │ │
│ │ Context Tokens: [2000]  ───●───         │ │
│ └─────────────────────────────────────────┘ │
│                                             │
│ Conversation                                │
│ ┌─────────────────────────────────────────┐ │
│ │ History: 5 turns (2.1k tokens)          │ │
│ │ [🗑️ Clear Chat]                         │ │
│ └─────────────────────────────────────────┘ │
│                                             │
│ Debug Info                                  │
│ ┌─────────────────────────────────────────┐ │
│ │ Instructions: 3 files (1.2k tokens)     │ │
│ │ Datasets: 5 files (4.3k tokens)         │ │
│ │ Index: 156 chunks                       │ │
│ └─────────────────────────────────────────┘ │
│                                             │
└─────────────────────────────────────────────┘
```

### Index Management Screen (📚)

```
┌─────────────────────────────────────────────────────────────────┐
│ 📚 Knowledge Index Management                            [×]    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│ Current Workspace: example-client                               │
│                                                                 │
│ Index Status                                                    │
│ ┌─────────────────────────────────────────────────────────────┐ │
│ │ Status:        ✅ Indexed                                   │ │
│ │ Chunks:        156                                          │ │
│ │ Last Updated:  2025-12-14 15:30                             │ │
│ │ Source:        ai-knowledge-{client}                        │ │
│ └─────────────────────────────────────────────────────────────┘ │
│                                                                 │
│ Actions                                                         │
│ ┌─────────────────────────────────────────────────────────────┐ │
│ │ [🔄 Rebuild Index]      Rebuild from compiled content       │ │
│ │ [🔨 Recompile Sources]  Recompile from source files         │ │
│ │ [🗑️ Clear Index]        Remove all indexed content          │ │
│ └─────────────────────────────────────────────────────────────┘ │
│                                                                 │
│ All Workspaces                                                  │
│ ┌─────────────────────────────────────────────────────────────┐ │
│ │ Workspace        │ Status    │ Chunks │ Last Updated        │ │
│ │─────────────────────────────────────────────────────────────│ │
│ │ Personal         │ ✅ Ready  │ 234    │ 2025-12-14 14:00   │ │
│ │ Company          │ ✅ Ready  │ 89     │ 2025-12-14 14:00   │ │
│ │ Client A         │ ✅ Ready  │ 156    │ 2025-12-14 15:30   │ │
│ │ Client B         │ ⚠️ Stale  │ 45     │ 2025-12-10 09:00   │ │
│ │ Client C         │ ❌ None   │ -      │ Never              │ │
│ └─────────────────────────────────────────────────────────────┘ │
│                                                                 │
│ [Index All Workspaces]                                          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Implementation Plan

### Phase 1: RAG as Default (Priority: High)

**Goal**: Make RAG work automatically without user intervention

1. **Enable RAG by default** in `ragbot_streamlit.py`
   - Change `use_rag = st.checkbox(..., value=False)` to `value=True`
   - Move RAG toggle out of Advanced Settings

2. **Auto-index on first query**
   - Check if index exists for workspace
   - If not, build index automatically (show progress indicator)
   - Cache index status to avoid repeated checks

3. **Update helpers.py**
   - Change `use_rag=False` default to `use_rag=True`
   - Add auto-index logic to `chat()` function

### Phase 2: Top Bar Layout (Priority: High)

**Goal**: Replace narrow sidebar with horizontal top bar

1. **Create new layout structure**
   - Use `st.columns()` for top bar elements
   - Remove sidebar (`st.sidebar`)
   - Use full page width for chat

2. **Implement top bar components**
   - Workspace selector (dropdown)
   - Model selector (dropdown with provider grouping)
   - Creativity selector (preset dropdown)
   - Settings button (opens modal)
   - Index button (opens index management)

3. **Style improvements**
   - Custom CSS for compact top bar
   - Responsive design for different screen sizes

### Phase 3: Settings Modal (Priority: Medium)

**Goal**: Replace sidebar settings with modal dialog

1. **Create modal component**
   - Use Streamlit's `st.dialog` or custom implementation
   - Organize settings into logical groups

2. **Settings groups**
   - Model Configuration (provider, model, max tokens)
   - RAG Configuration (toggle, context tokens)
   - Conversation (history stats, clear button)
   - Debug Info (file counts, token counts, index stats)

### Phase 4: Index Management Screen (Priority: Medium)

**Goal**: Dedicated screen for index operations

1. **Create index management page**
   - Could be a separate Streamlit page or modal
   - Show all workspaces and their index status

2. **Index operations**
   - Rebuild index from compiled content
   - Recompile from sources (trigger AI Knowledge Compiler)
   - Clear index
   - Index all workspaces

3. **Status indicators**
   - ✅ Ready: Index exists and is current
   - ⚠️ Stale: Index exists but source files are newer
   - ❌ None: No index exists

### Phase 5: Compiler Integration (Priority: Low)

**Goal**: Ability to recompile from sources within Ragbot

1. **Add compiler trigger**
   - Call AI Knowledge Compiler from Ragbot
   - Show compilation progress
   - Refresh index after compilation

2. **Watch mode** (future)
   - Detect source file changes
   - Auto-recompile and re-index

---

## Technical Considerations

### Streamlit Limitations

- No native modal support (use `st.dialog` in newer versions or custom CSS)
- Limited layout control (use `st.columns`, custom CSS)
- State management complexity (use session state carefully)

### RAG Auto-Indexing

- First query may be slow (indexing happens in background)
- Need progress indicator during indexing
- Consider lazy indexing (index on demand, not all at once)

### Mobile Responsiveness

- Top bar should collapse gracefully on mobile
- Consider hamburger menu for mobile

---

## Success Metrics

| Metric | Current | Target |
|--------|---------|--------|
| Steps to enable RAG | 3+ clicks | 0 (automatic) |
| Visible settings truncation | Yes | No |
| Chat area width | ~70% | ~95% |
| Time to first RAG query | Manual index required | Automatic |

---

## Timeline

| Phase | Estimated Effort | Dependencies |
|-------|------------------|--------------|
| Phase 1: RAG as Default | 2-3 hours | None |
| Phase 2: Top Bar Layout | 4-6 hours | Phase 1 |
| Phase 3: Settings Modal | 3-4 hours | Phase 2 |
| Phase 4: Index Management | 4-6 hours | Phase 1 |
| Phase 5: Compiler Integration | 6-8 hours | AI Knowledge Compiler |

---

## Open Questions

1. **Multi-page vs Single-page**: Should Index Management be a separate page or a modal?
2. **Keyboard Shortcuts**: Should we add keyboard shortcuts (Cmd+K for settings, etc.)?
3. **Theme Support**: Should we support dark/light themes?
4. **Mobile First**: How important is mobile support?

---

## References

- [Claude Desktop UI](https://claude.ai) - Reference for collapsible sidebar
- [ChatGPT UI](https://chatgpt.com) - Reference for clean chat interface
- [Streamlit Components](https://docs.streamlit.io/library/components) - Available UI elements
