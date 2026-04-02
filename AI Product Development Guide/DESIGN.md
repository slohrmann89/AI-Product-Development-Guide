# DESIGN.md

## name: medease-design-system
description: Design system rules for the MedEase / shadcn-based Figma file. Use this skill whenever creating, editing, or reviewing screens, components, or layouts in this Figma file. Enforces component reuse, typography styles, theme variables, auto layout, 4px grid, accessibility, and board organization. Apply before any use_figma call.

# MedEase Design System — Agent Rules

These rules govern all design work in the shadcn-based Figma design system file. They apply to every screen, component, and layout created or modified by an agent. Read this file completely before writing any Figma Plugin API code.

## 1. Component Architecture

### 1.1 Never draw what already exists

Before creating any UI element, search the Primitives and Components pages for an existing component. Use `findVariant()` on the component set and call `.createInstance()`. Do not recreate buttons, inputs, avatars, or any other element from raw frames and text nodes.

**Available Primitives** (page: `Primitives`, id `1:22`):

| Component Set | Variants | Use for |
| --- | --- | --- |
| `button` | 19 (default, outline, subtle, ghost, destructive, link, icon, loading) | All interactive buttons |
| `input` | 13 (sizes, states, types) | Form inputs, search bars |
| `textarea` | 4 | Multi-line text entry |
| `avatar` | 2 (initials, image) | User/patient avatars |
| `tab item` | 2 (selected, unselected) | Tab navigation |
| `table item` | 3 (head, row default, row selected) | Data tables |
| `accordion Item` | 2 (open, closed) | Expandable sections |
| `checkbox` | 2 (default, with text) | Form checkboxes |
| `radio button` | 2 (default, selected) | Radio selections |
| `switch` | 2 (on, off) | Toggle controls |
| `menu item` | 4 | Dropdown/context menus |
| `collapsible` | 2 (open, closed) | Collapsible panels |
| `scroll list item` | 1 | Scrollable lists |
| `separator` | — | Visual dividers |
| `inline code` | 1 | Code snippets |
| `navigation menu item` | 4 | Top nav items |

**Available Composed Components** (page: `Components`, id `4:6598`):

separator, slider, tabs and content, tooltip, popover, progress, radio group, scroll-area, hover card, label, menubar and content, navigation menu and content, command, context menu, dialog, dropdown menu, accordion, alert dialog

### 1.2 How to instantiate local components

Local components cannot use `importComponentByKeyAsync`. Instead:

```jsx
const page = await figma.getNodeByIdAsync('1:22');
const buttonSet = page.children.find(c => c.name === 'button');

function findVariant(compSet, props) {
  for (const child of compSet.children) {
    let matches = true;
    for (const [key, val] of Object.entries(props)) {
      if (!child.name.toLowerCase().includes(
        `${key.toLowerCase()}=${val.toLowerCase()}`
      )) { matches = false; break; }
    }
    if (matches) return child;
  }
  return compSet.children[0];
}

const btnDefault = findVariant(buttonSet, { type: 'default', state: 'default' });
const instance = btnDefault.createInstance();
```

### 1.3 Overriding text in instances

Use `findOne` or `findAll` to locate TEXT nodes inside an instance:

```jsx
function setFirstText(instance, newText) {
  const t = instance.findOne(n => n.type === 'TEXT');
  if (t) t.characters = newText;
}
```

Do not detach instances to change text. Always override via the instance API.

### 1.4 Component property conventions

When creating new component sets, follow the existing naming pattern:

- Variant axes use `key=value` format: `type=default, state=Default`
- State variants: `Default`, `hover`, `focused`, `disabled`, `selected`
- Type variants: `default`, `outline`, `subtle`, `ghost`, `destructive`, `link`
- Size variants: `default`, `sm`, `lg`

## 2. Color and Theming

### 2.1 Theme Variable Collection

All semantic colors live in the `Theme` variable collection with two modes: **Light** and **Dark**.

| Token | Light | Dark | Use for |
| --- | --- | --- | --- |
| `background` | #f9fbfa | #05140a | Page/screen backgrounds |
| `foreground` | #05140a | #f9fbfa | Primary text |
| `card` | #ffffff | #05140a | Card surfaces |
| `card-foreground` | #05140a | #f9fbfa | Text on cards |
| `popover` | #ffffff | #05140a | Popover/dropdown surfaces |
| `popover-foreground` | #05140a | #f9fbfa | Text in popovers |
| `muted` | #e3e8e5 | #173621 | Subtle backgrounds, breaks |
| `muted-foreground` | #5c7063 | #9dafa3 | Secondary/meta text |
| `primary` | #0f3e1b | #f9fbfa | Primary actions, filled buttons |
| `primary-foreground` | #f9fbfa | #0f3e1b | Text on primary surfaces |
| `secondary` | #e4f1e9 | #173621 | Secondary surfaces, badges |
| `secondary-foreground` | #0f3e1b | #f9fbfa | Text on secondary surfaces |
| `accent` | #e4f1e9 | #173621 | Hover states, highlights |
| `accent-foreground` | #0f3e1b | #f9fbfa | Text on accent surfaces |
| `destructive` | #ef4444 | #7f1d1d | Errors, danger actions |
| `destructive-foreground` | #fafafa | #f9fbfa | Text on destructive surfaces |
| `border` | #e2e9e4 | #173621 | Borders, dividers |
| `input` | #e2e9e4 | #173621 | Input field borders |
| `ring` | #0f3e1b | #f9fbfa | Focus rings |

### 2.2 Mandatory: bind to variables, never hardcode

Every fill and stroke must be bound to a Theme variable using `setBoundVariableForPaint`:

```jsx
function solidPaint(varName) {
  return figma.variables.setBoundVariableForPaint(
    { type: 'SOLID', color: { r: 0, g: 0, b: 0 },
      visible: true, opacity: 1, blendMode: 'NORMAL' },
    'color', varMap[varName]
  );
}

node.fills = [solidPaint('primary')];
```

Never use `hex()` or raw RGB values for semantic colors. Raw colors are only acceptable for Tailwind primitive swatches on the Colors page.

### 2.3 Paint styles

Semantic paint styles exist under the `theme/` prefix (e.g. `theme/primary`, `theme/background`). These are bound to the variable collection and switch automatically between Light/Dark.

Tailwind primitive palettes (`slate/50`...`rose/900`, `white`, `black`) are available as paint styles for reference but should not be used directly in screens. Always prefer the semantic `theme/*` tokens.

### 2.4 Sidebar convention

Sidebars use `primary` as fill. All text and icons inside sidebars use `primary-foreground`. Active nav items use a white overlay at 12% opacity. Inactive dots use white at 30% opacity.

## 3. Typography

### 3.1 Always assign a text style

Every text node must have a `textStyleId` set from the design system. Do not create text with freestyle font sizes.

```jsx
const style = figma.getLocalTextStyles().find(s => s.name === 'body-medium');
textNode.textStyleId = style.id;
```

### 3.2 Text style inventory

| Style | Size/LH | Weight | Use for |
| --- | --- | --- | --- |
| `h1` | 48/48 | Extra Bold | Hero headings (rare in dashboards) |
| `h2` | 30/36 | Semi Bold | Large metric values, section headings |
| `h3` | 24/32 | Semi Bold | Page titles ("Scheduling", "Overview") |
| `h4` | 20/28 | Semi Bold | Card group titles, patient names |
| `large` | 18/28 | Semi Bold | Emphasized UI labels |
| `lead` | 20/28 | Regular | Subtitles, descriptive text |
| `p` | 16/28 | Regular | Paragraph body text |
| `p-ui` | 16/24 | Regular | UI body text (tighter LH) |
| `p-ui-medium` | 16/24 | Medium | Emphasized UI labels |
| `body` | 14/24 | Regular | Standard body text |
| `body-medium` | 14/24 | Medium | Names in lists, labels |
| `subtle` | 14/20 | Regular | Secondary information |
| `subtle-medium` | 14/20 | Medium | Compact labels |
| `suble - semibold` | 14/20 | Semi Bold | Card section titles |
| `small` | 14/14 | Medium | Tight labels |
| `detail` | 12/20 | Medium | Badges, timestamps, meta text |
| `table head` | 16/24 | Bold | Table column headers |
| `table item` | 16/24 | Regular | Table cell text |

### 3.3 Mapping guide for common UI patterns

- Page title → `h3`
- KPI large number → `h2`
- Section/card title → `suble - semibold`
- Patient name in list → `body-medium`
- Timestamp, meta info → `detail`
- Status badge text → `detail`
- Nav item label → `body` (regular) or `suble - semibold` (active)
- Subtitle/date → `body` with `muted-foreground`

### 3.4 Font family

All text uses **Inter**. Load all needed weights before creating text:

```jsx
await figma.loadFontAsync({ family: 'Inter', style: 'Regular' });
await figma.loadFontAsync({ family: 'Inter', style: 'Medium' });
await figma.loadFontAsync({ family: 'Inter', style: 'Semi Bold' });
await figma.loadFontAsync({ family: 'Inter', style: 'Bold' });
await figma.loadFontAsync({ family: 'Inter', style: 'Extra Bold' });
```

Note: The style is `"Semi Bold"` (with space), not `"SemiBold"`. Same for `"Extra Bold"`.

## 4. Layout and Spacing

### 4.1 Auto Layout everywhere

Every frame must use Auto Layout. No absolute positioning except for overlay elements (dialogs, tooltips). Set `layoutMode` to `'VERTICAL'` or `'HORIZONTAL'` on every frame you create.

### 4.2 The 4px grid

All spacing, padding, and sizing values must be multiples of 4:

- **Padding**: 4, 8, 12, 16, 20, 24, 28, 32
- **Item spacing (gap)**: 2, 4, 8, 12, 16, 20, 24
- **Component height**: 24, 28, 32, 36, 40, 48, 56
- **Border radius**: 4, 8, 12, 16 (default: 8 = `-radius: 0.5rem`)

Never use odd numbers or values not divisible by 4 (exceptions: 2px for tight gaps, border-width of 1px).

### 4.3 Standard screen dimensions

- Desktop: **1440 x 900** (primary viewport)
- Tablet: **768 x 1024**
- Mobile: **375 x 812**

### 4.4 Dashboard layout pattern

```
┌─────────┬──────────────────────────────────┐
│         │  Header (greeting + actions)      │
│ Sidebar │──────────────────────────────────│
│  240px  │  Content                         │
│         │  ┌──────────┬──────────┐         │
│ primary │  │  Main    │  Side    │         │
│  fill   │  │  (grow)  │  (fixed) │         │
│         │  └──────────┴──────────┘         │
└─────────┴──────────────────────────────────┘
```

- Sidebar: 240px fixed, `primary` fill, vertical nav with button instances
- Main content: `layoutGrow = 1`, padding 28/32, `background` fill
- Content area: horizontal split, gap 16-20

### 4.5 Card anatomy

```jsx
const card = figma.createFrame();
card.layoutMode = 'VERTICAL';
card.fills = [solidPaint('card')];
card.strokes = [solidPaint('border')];
card.strokeWeight = 1;
card.cornerRadius = 8;
card.paddingTop = 16;
card.paddingBottom = 16;
card.paddingLeft = 16;
card.paddingRight = 16;
card.itemSpacing = 12;
```

### 4.6 Sizing modes

- Containers: `primaryAxisSizingMode = 'FIXED'`, `counterAxisSizingMode = 'AUTO'` (or FIXED for full-bleed)
- Growing children: `layoutGrow = 1`
- Stretching children: `layoutAlign = 'STRETCH'`
- Never set explicit width/height on children that should flex

## 5. Information Architecture

### 5.1 Screen hierarchy

Each screen follows this content hierarchy:

1. **Header bar**: Page title (h3) + subtitle (body, muted-foreground) + action buttons
2. **Navigation/tabs**: Tab item instances for sub-sections
3. **Content area**: Two-column or single-column layout
4. **Cards**: Grouped content blocks with clear titles (suble-semibold) and consistent padding

### 5.2 Data density rules

- KPI cards: max 4 per row, show value (h2) + label (detail) + trend (detail)
- Tables: use table item component instances, not custom rows
- Lists: avatar + two-line text (name as body-medium, meta as detail) + action/badge
- Limit vertical content to what fits in the viewport without excessive scrolling

### 5.3 Status and state patterns

Use badge patterns consistently:

| Status | Background | Foreground |
| --- | --- | --- |
| Active/Confirmed | `secondary` | `secondary-foreground` |
| Pending/Neutral | `muted` | `muted-foreground` |
| Urgent/Error | `destructive` | `destructive-foreground` |
| New/Info | `primary` | `primary-foreground` |

Badge construction: cornerRadius 12, padding 3/8, text style `detail`.

## 6. Accessibility

### 6.1 Color contrast

All text must meet WCAG 2.1 AA contrast ratios:

- Normal text (< 18px): minimum 4.5:1
- Large text (>= 18px bold or >= 24px): minimum 3:1
- The Evergreen theme is designed to meet these requirements in both Light and Dark modes

### 6.2 Touch targets

Interactive elements must have a minimum tap target of 44x44px (mobile) or 32x32px (desktop). Button components already satisfy this.

### 6.3 Focus indicators

All interactive elements must support visible focus states using the `ring` variable for focus ring color, 2px offset.

### 6.4 Semantic layer naming

Name layers descriptively for screen reader compatibility:

- `nav/Overview`, `slot/09:00`, `stat/Confirmed` — not `Frame 47` or `Group 12`
- Use slash-separated naming: `category/specific`

### 6.5 Color independence

Never use color as the sole indicator of state. Always pair color with text labels, icons, or patterns (e.g. "Urgent" badge text + red background, not just red dot).

## 7. Board Organization

### 7.1 Page structure

| Page | Purpose |
| --- | --- |
| `Cover` | File cover thumbnail |
| `Components` | Composed component showcases + screen designs |
| `Typography` | Type scale reference |
| `Colors` | Color palette reference |
| `Primitives` | Atomic component masters (DO NOT EDIT unless intentional) |
| `Icons` | Icon library |

### 7.2 Screen placement

- Place screens left-to-right in reading order
- 60px horizontal gap between screens
- Group related screens (same flow) in a horizontal row
- Separate flows vertically with 120px gap
- Name every screen frame: `Dashboard — [Section Name]`

### 7.3 Frame naming conventions

```
Screen level:   "Dashboard — Overview"
Section level:  "Header", "Content", "Sidebar"
Card level:     "KPI/Patients Today", "Patient Queue"
Row level:      "slot/09:00", "appt/Maria Schmid"
Component:      "nav/Scheduling", "stat/Confirmed", "badge/Urgent"
```

### 7.4 Do not modify Primitives

The Primitives page contains component masters. All changes to component design happen here and propagate to instances. Never edit a component on the Primitives page to fix a single screen. Instead, create a variant if a new state is needed.

## 8. Handoff and Developer Notes

### 8.1 CSS variable mapping

The Theme variable collection maps 1:1 to shadcn/ui CSS custom properties:

```css
--background    →  Theme/background
--foreground    →  Theme/foreground
--primary       →  Theme/primary
--secondary     →  Theme/secondary
--muted         →  Theme/muted
--accent        →  Theme/accent
--destructive   →  Theme/destructive
--border        →  Theme/border
--input         →  Theme/input
--ring          →  Theme/ring
/* Each with -foreground variant */
```

### 8.2 Border radius token

- `-radius: 0.5rem` (8px). Derived values:
- `lg`: 8px (var(--radius))
- `md`: 6px (calc(var(--radius) - 2px))
- `sm`: 4px (calc(var(--radius) - 4px))

### 8.3 Component-to-code mapping

| Figma Component | Code Component |
| --- | --- |
| `button` (type=default) | `<Button>` |
| `button` (type=outline) | `<Button variant="outline">` |
| `button` (type=destructive) | `<Button variant="destructive">` |
| `button` (type=subtle) | `<Button variant="secondary">` |
| `button` (type=ghost) | `<Button variant="ghost">` |
| `input` | `<Input>` |
| `textarea` | `<Textarea>` |
| `switch` | `<Switch>` |
| `checkbox` | `<Checkbox>` |
| `avatar` | `<Avatar>` |
| `tab item` | `<TabsTrigger>` |
| `table item` (head) | `<TableHead>` |
| `table item` (row) | `<TableCell>` |
| `accordion Item` | `<AccordionItem>` |
| `dialog` | `<Dialog>` |
| `popover` | `<Popover>` |
| `progress` | `<Progress>` |
| `separator` | `<Separator>` |

### 8.4 Spacing tokens for developers

| Figma padding/gap | Tailwind class |
| --- | --- |
| 4px | `p-1` / `gap-1` |
| 8px | `p-2` / `gap-2` |
| 12px | `p-3` / `gap-3` |
| 16px | `p-4` / `gap-4` |
| 20px | `p-5` / `gap-5` |
| 24px | `p-6` / `gap-6` |
| 28px | `p-7` / `gap-7` |
| 32px | `p-8` / `gap-8` |

## 9. Agent Checklist

Before completing any screen design, verify:

- [ ]  All UI elements are component instances (not hand-drawn)
- [ ]  All text nodes have a `textStyleId` from the typography scale
- [ ]  All fills/strokes are bound to Theme variables (no hardcoded hex)
- [ ]  All frames use Auto Layout (`layoutMode` set)
- [ ]  All spacing values are multiples of 4px
- [ ]  Border radius is 8px (unless 4px or 12px is contextually correct)
- [ ]  Screen frame is named descriptively ("Dashboard — [Name]")
- [ ]  All layers have semantic names (no "Frame 1", "Group 2")
- [ ]  Color contrast meets WCAG AA
- [ ]  Interactive elements meet minimum tap target size
- [ ]  Status indicators use color + text label, never color alone