# Anatomica

**Anatomica** is a tiny, text-first notation for describing the anatomy of software products.

It models **Screens, Components, Actions, States, and Flows** so teams can design, discuss, and evolve products before pixels or code. Anatomica is to software what Leonardo da Vinci’s notebooks were to anatomy: a way to expose structure, motion, and hidden order.

> **Grammar (one line):**  
> `[Screen]::Component.SubComponent@state.action() => [dest] | ::Component | .action() | @state`

## Why Anatomica?

- **Clarity** — make the full surface area of a product visible  
- **Cohesion** — one shared language across product, design, and engineering  
- **Cadence** — versionable artifacts (PRs, diffs) that evolve with the product

## Quick Cheat Sheet

```
[Screen]                      # screen
[Screen@state]                # screen in a state
[Screen]::Component           # component on a screen
::Component.SubComponent      # nested component
::Component@state             # component in a state
::Component.action()          # action on a component
.action() => [Screen]         # navigate to a screen
.action() => ::Component      # focus/open a component (e.g., modal)
.action() => .action()        # chain to another action
.action() => @state           # transition to a state
// comment                    # commentary above a line
```

## Core Concepts

### Screens

Top-level product surfaces (pages, key contexts).

```
[Inbox]
[MessageDetail]
[SentItems]
[Drafts]
```

### Components

Structural parts that make up a screen. Nest with `.` for subcomponents.

```
[Inbox]::MessageList
[Inbox]::MessageListItem
[MessageDetail]::Toolbar
[Inbox]::ComposeButton
```

### Actions

User or system behaviors tied to a component. Start with a dot.

```
[Inbox]::MessageListItem.open()
[Inbox]::MessageListItem.archive()
[Inbox]::ComposeButton.click()
```

### States

UI or system conditions that affect rendering or behavior.

```
[Inbox@empty]
[Inbox@ready]
::NewMessageModal@open
::NewMessageModal@closed
```

### Flows

Directed sequences showing movement between screens, components, or states. Use `=>`.

```
[Inbox]::MessageListItem.open()       => [MessageDetail]
[MessageDetail]::Toolbar.reply()      => ::NewMessageModal@open
::NewMessageModal.send()              => [SentItems]
```

### Indented Style

Use indentation to show hierarchy and avoid repetition.

```
[Inbox]
  ::MessageList
    ::MessageListItem
      .open()              => [MessageDetail]
      .archive()
  ::ComposeButton
    .click()               => ::NewMessageModal@open
```

### Modals

Model modals as components with `@open`/`@closed` states.

```
::NewMessageModal
  @closed
    .open()                => @open
  @open
    .typeRecipient()
    .typeBody()
    .send()                => [Inbox]
    .close()               => @closed
```

### Lists & Items

Represent both lists and their items as components.

```
[Inbox]
  ::MessageList
    @empty
    @ready
  ::MessageListItem
    .open()                => [MessageDetail]
    .archive()
    .markRead()
```

### Comments & Overlays

Use comments above lines to capture insights.

```
[Inbox]
  // Say: users often struggle to find archived mail
  // Do: heavy use of search instead of archive
  // Focus: improve visibility of Archive
  ::MessageListItem.archive()
```

### Events

The annotation maps perfectly to event names in analytics.

```
[Inbox]
  ::MessageListItem
    .open()
    .archive()
    .markRead()

# Events
inbox.message_list_item.open
inbox.message_list_item.archive
inbox.message_list_item.mark_read
```

## Full Examples

### Email Client Flow

```
[Inbox]
  ::MessageList
    ::MessageListItem
      .open()              => [MessageDetail]
      .archive()
  ::ComposeButton
    .click()               => ::NewMessageModal@open

::NewMessageModal
  @closed
    .open()                => @open
  @open
    .typeRecipient()
    .typeBody()
    .send()                => [SentItems]
    .close()               => @closed

[MessageDetail]
  ::Toolbar
    .reply()               => ::NewMessageModal@open
    .archive()             => [Inbox]
```

This simple snippet captures:

- Screens: `Inbox`, `MessageDetail`, `SentItems`
- Components: `MessageList`, `MessageListItem`, `ComposeButton`, `NewMessageModal`, `Toolbar`
- Actions: `.open()`, `.archive()`, `.send()`, `.reply()`
- States: `@empty`, `@ready`, `@open`, `@closed`
- Flows: navigation between `Inbox → MessageDetail`, `Compose → SentItems`
