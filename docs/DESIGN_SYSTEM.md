# LioPGP Material Design 3 System

## Overview
LioPGP implements Material Design 3 (Material You) with custom theming for cryptographic certificate management. The design system emphasizes clarity, security affordances, and accessibility.

## Design Principles

1. **Security-First Clarity**: Visual hierarchy prioritizes security-critical information
2. **Trust Through Transparency**: Certificate status and key information always visible
3. **Accessibility**: WCAG 2.1 AA compliance minimum
4. **Performance**: Smooth animations at 60fps+ on mid-range devices
5. **Adaptability**: Responsive layouts for phones, tablets, and foldables

## Color System

### Core Palette (Dynamic Theming)

**Primary Color**: Security Blue
- Light: #006FDC
- Dark: #D0E8FF
- Container (Light): #D0E8FF
- Container (Dark): #004E9B
- On Primary (Light): #FFFFFF
- On Primary (Dark): #FFFFFF

**Secondary Color**: Trust Teal
- Light: #006B5E
- Dark: #4FD7C8
- Container (Light): #A0F0E8
- Container (Dark): #004E47

**Tertiary Color**: Accent Purple
- Light: #7C5800
- Dark: #FFD98D

### Key Type Colors (Semantic)

| Algorithm | Color | Usage |
|-----------|-------|-------|
| RSA | #0066CC (Blue) | Traditional asymmetric |
| ECC/EdDSA | #7C3AED (Purple) | Elliptic curve |
| PQC (ML-KEM) | #10B981 (Green) | Post-quantum safe |
| Expired/Invalid | #EF4444 (Red) | Error state |
| Untrusted | #F59E0B (Amber) | Warning state |

### Surface Colors

```
Surface:        #FFFBFE (Light) / #1C1B1F (Dark)
Surface Dim:    #DED8E1 (Light) / #0F0D13 (Dark)
Surface Bright: #FFFBFE (Light) / #2B2930 (Dark)
Outline:        #79747E (Light) / #CAC7D0 (Dark)
Outline Variant:#CAC7D0 (Light) / #49454E (Dark)
```

## Typography

### Font Family
- **Primary**: Roboto (system default)
- **Alternative**: Inter (if custom branding needed)

### Type Scale

| Style | Size | Weight | Line Height | Letter Spacing |
|-------|------|--------|-------------|----------------|
| Display Large | 57sp | 400 | 64sp | 0sp |
| Display Medium | 45sp | 400 | 52sp | 0sp |
| Display Small | 36sp | 400 | 44sp | 0sp |
| Headline Large | 32sp | 400 | 40sp | 0sp |
| Headline Medium | 28sp | 400 | 36sp | 0sp |
| Headline Small | 24sp | 400 | 32sp | 0sp |
| Title Large | 22sp | 500 | 28sp | 0sp |
| Title Medium | 16sp | 500 | 24sp | 0.15sp |
| Title Small | 14sp | 500 | 20sp | 0.1sp |
| Body Large | 16sp | 400 | 24sp | 0.5sp |
| Body Medium | 14sp | 400 | 20sp | 0.25sp |
| Body Small | 12sp | 400 | 16sp | 0.4sp |
| Label Large | 14sp | 500 | 20sp | 0.1sp |
| Label Medium | 12sp | 500 | 16sp | 0.5sp |
| Label Small | 11sp | 500 | 16sp | 0.5sp |

## Components & Patterns

### Key Card Component
```
┌─────────────────────────────────────┐
│ ⊕ [Algorithm Badge]  [Status Icon]  │
├─────────────────────────────────────┤
│ Key Name / Email                    │
│ Fingerprint: ABC123...XYZ           │
│ Created: 2025-05-28  | Expires: ... │
├─────────────────────────────────────┤
│ [Trust Level] [Use] [Delete]        │
└─────────────────────────────────────┘
```

**Elevation**: 1dp (default) → 3dp (pressed)
**Corners**: 12dp border radius
**Padding**: 16dp (horizontal), 12dp (vertical)
**States**: Default, Pressed, Hover, Focus, Disabled

### Certificate Detail Sheet
- Full-screen modal on phones
- Side sheet on tablets (50% width)
- Animation: Slide up from bottom (300ms)

### Action Buttons

**FAB (Floating Action Button)**
- Primary: Create new key
- Size: 56dp (fab) / 40dp (mini-fab)
- Icon: + or relevant symbol
- Extended FAB for "Import Key" action

**Standard Buttons**
```
Filled:      Primary color, full width
Outlined:    Border only, secondary action
Text:        Minimal, tertiary action
Elevated:    Raised, for secondary CTAs
```

## Spacing & Layout

**Base Unit**: 4dp

| Size | Value | Usage |
|------|-------|-------|
| xs | 4dp | Internal icon spacing |
| sm | 8dp | Dense layouts |
| md | 16dp | Standard padding |
| lg | 24dp | Section spacing |
| xl | 32dp | Major sections |
| xxl | 48dp | Screen margins |

**Grid**: 4-column (phone) → 8-column (tablet) → 12-column (desktop)

## Motion & Animation

### Easing Curves
- **Standard**: cubic-bezier(0.2, 0, 0, 1)
- **Emphasized**: cubic-bezier(0.3, 0, 0.8, 0.15)
- **Decelerated**: cubic-bezier(0, 0, 0.2, 1)
- **Accelerated**: cubic-bezier(0.4, 0, 1, 1)

### Timing
- **Short**: 150ms (state changes, icons)
- **Medium**: 300ms (card expand, transitions)
- **Long**: 500ms (page transitions, dialogs)

## Accessibility

### Contrast Ratios
- Text on background: 4.5:1 (normal) / 3:1 (large)
- UI components: 3:1 minimum

### Touch Targets
- Minimum: 48dp × 48dp
- Preferred: 56dp × 56dp

### States
- Focus: 4dp outline, 2px stroke
- Disabled: 38% opacity
- Hover: 8% overlay (light mode) / 12% (dark mode)

## Responsive Breakpoints

| Screen | Width | Layout | Columns |
|--------|-------|--------|---------|
| Phone | < 600dp | Single pane | 4 |
| Tablet | 600-840dp | Split pane | 8 |
| Desktop | > 840dp | Multi-pane | 12 |

## Implementation

See `app/src/main/res/values/themes.xml` for color definitions.
See `app/src/main/res/values/dimen.xml` for spacing/sizing.
