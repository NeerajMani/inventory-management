---
name: saas-ui-redesign
description: Transform Vue 3 app from horizontal top navigation to modern SaaS-style interface with vertical left sidebar. Use this skill when redesigning UI layout or modernizing navigation structure.
---

# SaaS UI Redesign - Vertical Sidebar Navigation

This skill provides comprehensive guidelines for transforming a Vue 3 application from horizontal top navigation to a modern SaaS-style interface with a vertical left sidebar. Follow these patterns to create a professional, scalable navigation structure.

## Overview

**Purpose**: Transform horizontal navigation bar into a vertical left sidebar with consistent spacing and polished professional aesthetics.

**Target Applications**: Vue 3 applications with horizontal top navigation that need:
- Better scalability (more navigation items)
- Modern SaaS interface aesthetics
- Improved visual hierarchy
- Professional, polished look

**Key Benefits**:
- Scalable navigation (easily accommodates new routes)
- Better visual hierarchy with vertical flow
- Consistent spacing throughout the application
- Professional SaaS-style interface
- Room for future growth

## When to Use This Skill

Use this skill when:
- User requests "modern SaaS UI" or "vertical sidebar navigation"
- Current horizontal navigation is becoming crowded
- Need to modernize application appearance
- Want professional, polished interface
- Improving navigation scalability
- Redesigning layout structure

## CRITICAL: Vue-Expert Delegation Rule

**⚠️ MANDATORY RULE from CLAUDE.md:**

> "ANY time you need to create or significantly modify a .vue file, you MUST delegate to vue-expert"

**All Vue file work MUST be delegated to the vue-expert agent:**
- Creating new components (Sidebar.vue)
- Modifying App.vue layout and styles
- Updating router configuration in main.js
- Any changes to .vue files

**Example delegation:**
```
I need to delegate this UI transformation to vue-expert agent:

Agent({
  subagent_type: "vue-expert",
  description: "Create SaaS-style sidebar navigation",
  prompt: "Transform the application from horizontal nav to vertical sidebar...
  [Include specific instructions, file paths, and code templates]"
})
```

## Layout Transformation Approach

### Target Architecture

```
┌─────────────┬────────────────────────────────────┐
│   Sidebar   │       Main Content Area            │
│   (260px)   │                                    │
│             │  ┌──────────────────────────────┐  │
│  ┌───────┐  │  │     Filter Bar (optional)    │  │
│  │ Logo  │  │  └──────────────────────────────┘  │
│  └───────┘  │                                    │
│             │  ┌──────────────────────────────┐  │
│  Nav Items: │  │                              │  │
│  • Dashboard│  │      Page Content            │  │
│  • Inventory│  │      (router-view)           │  │
│  • Orders   │  │                              │  │
│  • Backlog  │  │                              │  │
│  • Finance  │  │                              │  │
│  • Demand   │  │                              │  │
│  • Reports  │  │                              │  │
│             │  └──────────────────────────────┘  │
└─────────────┴────────────────────────────────────┘
```

### Key Design Decisions

1. **Sidebar Width**: 260px fixed (standard SaaS pattern)
2. **Component Structure**: Separate `Sidebar.vue` component (clean separation of concerns)
3. **Positioning**: Fixed left sidebar, main content shifts right
4. **Filter Bar**: Remains functional, positioned within main-wrapper
5. **Scrolling**: Independent scroll for sidebar and main content
6. **Z-index**: Sidebar (100), Filter bar (90), Modals (existing values)

## Implementation Steps

Follow these steps in order, delegating Vue work to vue-expert agent:

### Step 1: Create Sidebar Component

**Delegate to vue-expert** to create `client/src/components/Sidebar.vue`

**Requirements:**
- Logo/branding at top of sidebar
- All navigation routes listed vertically
- Active route highlighting
- Hover states
- Proper spacing and typography
- Use code template from section below

### Step 2: Update App.vue Layout

**Delegate to vue-expert** to modify `client/src/App.vue`

**Changes needed:**
- Remove horizontal nav bar (current lines 3-35)
- Import and add Sidebar component
- Update layout structure: flex container with sidebar + main-wrapper
- Move FilterBar inside main-wrapper
- Adjust z-index and positioning

### Step 3: Add Missing Route (if applicable)

**Delegate to vue-expert** to modify `client/src/main.js`

**Add Backlog route** (currently exists as Backlog.vue but not routed):
- Import Backlog component
- Add route to router configuration
- Ensure it appears in sidebar navigation

### Step 4: Update Global Styles

**Delegate to vue-expert** to modify styles in `client/src/App.vue`

**Style updates:**
- Remove old top-nav styles
- Add main-wrapper styles
- Update main-content positioning (margin-left: 260px)
- Adjust FilterBar positioning
- Ensure consistent spacing throughout

## Code Templates

### Template 1: Sidebar.vue Component

```vue
<template>
  <aside class="sidebar">
    <div class="sidebar-header">
      <h1 class="logo">{{ t('nav.companyName') }}</h1>
      <span class="subtitle">{{ t('nav.subtitle') }}</span>
    </div>
    
    <nav class="sidebar-nav">
      <router-link to="/" class="nav-item">
        <span class="nav-label">{{ t('nav.overview') }}</span>
      </router-link>
      <router-link to="/inventory" class="nav-item">
        <span class="nav-label">{{ t('nav.inventory') }}</span>
      </router-link>
      <router-link to="/orders" class="nav-item">
        <span class="nav-label">{{ t('nav.orders') }}</span>
      </router-link>
      <router-link to="/backlog" class="nav-item">
        <span class="nav-label">Backlog</span>
      </router-link>
      <router-link to="/spending" class="nav-item">
        <span class="nav-label">{{ t('nav.finance') }}</span>
      </router-link>
      <router-link to="/demand" class="nav-item">
        <span class="nav-label">{{ t('nav.demandForecast') }}</span>
      </router-link>
      <router-link to="/reports" class="nav-item">
        <span class="nav-label">Reports</span>
      </router-link>
    </nav>
  </aside>
</template>

<script setup>
import { useI18n } from '../i18n'
const { t } = useI18n()
</script>

<style scoped>
.sidebar {
  width: 260px;
  height: 100vh;
  position: fixed;
  left: 0;
  top: 0;
  background: #0f172a;
  color: white;
  display: flex;
  flex-direction: column;
  z-index: 100;
  overflow-y: auto;
}

.sidebar-header {
  padding: 1.5rem;
  border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.logo {
  font-size: 1.25rem;
  font-weight: 700;
  margin: 0;
  color: white;
}

.subtitle {
  font-size: 0.8rem;
  opacity: 0.7;
  display: block;
  margin-top: 0.25rem;
}

.sidebar-nav {
  flex: 1;
  padding: 1rem 0;
}

.nav-item {
  display: flex;
  align-items: center;
  padding: 0.75rem 1.5rem;
  color: #cbd5e1;
  text-decoration: none;
  transition: all 0.2s;
  border-left: 3px solid transparent;
}

.nav-item:hover {
  background: rgba(255, 255, 255, 0.05);
  color: white;
}

.nav-item.router-link-active {
  background: rgba(59, 130, 246, 0.1);
  color: #60a5fa;
  border-left-color: #3b82f6;
}

.nav-label {
  font-size: 0.938rem;
  font-weight: 500;
}
</style>
```

### Template 2: Updated App.vue Layout Structure

```vue
<template>
  <div class="app">
    <Sidebar />
    
    <div class="main-wrapper">
      <FilterBar />
      <main class="main-content">
        <router-view />
      </main>
    </div>

    <!-- Keep existing modals -->
    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />

    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @toggle-task="toggleTask"
      @delete-task="deleteTask"
    />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import Sidebar from './components/Sidebar.vue'
import FilterBar from './components/FilterBar.vue'
import ProfileDetailsModal from './components/ProfileDetailsModal.vue'
import TasksModal from './components/TasksModal.vue'
import { api } from './api'

// Keep existing script setup code...
</script>

<style>
/* Reset and base styles */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background: #f8fafc;
  color: #0f172a;
  line-height: 1.6;
}

.app {
  display: flex;
  min-height: 100vh;
}

.main-wrapper {
  flex: 1;
  margin-left: 260px; /* Sidebar width */
  display: flex;
  flex-direction: column;
}

.main-content {
  flex: 1;
  padding: 1.5rem 2rem;
  max-width: 1600px;
  margin: 0 auto;
  width: 100%;
}

/* Keep all other existing styles (cards, tables, badges, etc.) */
/* ... */
</style>
```

### Template 3: Router Configuration with Backlog Route

```javascript
// In client/src/main.js
import { createApp } from 'vue'
import { createRouter, createWebHistory } from 'vue-router'
import App from './App.vue'

// Import views
import Dashboard from './views/Dashboard.vue'
import Inventory from './views/Inventory.vue'
import Orders from './views/Orders.vue'
import Backlog from './views/Backlog.vue'  // ADD THIS
import Spending from './views/Spending.vue'
import Demand from './views/Demand.vue'
import Reports from './views/Reports.vue'

const routes = [
  { path: '/', component: Dashboard },
  { path: '/inventory', component: Inventory },
  { path: '/orders', component: Orders },
  { path: '/backlog', component: Backlog },  // ADD THIS
  { path: '/spending', component: Spending },
  { path: '/demand', component: Demand },
  { path: '/reports', component: Reports }
]

const router = createRouter({
  history: createWebHistory(),
  routes
})

const app = createApp(App)
app.use(router)
app.mount('#app')
```

### Template 4: FilterBar Adjustments

```vue
<style scoped>
.filter-bar {
  position: sticky;
  top: 0;
  z-index: 90;
  background: #f8fafc;
  border-bottom: 1px solid #e2e8f0;
  padding: 1rem 2rem;
  display: flex;
  gap: 1rem;
  align-items: center;
  justify-content: space-between;
}

/* Keep existing filter styles */
</style>
```

## Design System Consistency

### Preserve Existing Design System

**Colors (from CLAUDE.md):**
- Primary dark: `#0f172a` (headings, sidebar background)
- Medium gray: `#64748b` (secondary text)
- Light gray: `#e2e8f0` (borders, dividers)
- Background: `#f8fafc` (page background)
- White: `#ffffff` (cards, content areas)

**Status Colors:**
- Success/Green: `#059669`, `#10b981`, `#d1fae5`
- Warning/Yellow: `#ea580c`, `#f59e0b`, `#fed7aa`
- Danger/Red: `#dc2626`, `#ef4444`, `#fecaca`
- Info/Blue: `#2563eb`, `#3b82f6`, `#dbeafe`

**Typography:**
- Font family: 'Inter', system fonts fallback
- Page title: 1.875rem (30px), weight 700
- Card title: 1.125rem (18px), weight 700
- Body text: 0.938rem (15px)
- Labels: 0.75rem (12px), uppercase, weight 600

**Spacing:**
- Section margin: 1.5rem
- Card padding: 1.25rem
- Grid gap: 1.25rem
- Border radius: 6px (inputs), 10px (cards)

### New Sidebar-Specific Colors

**Sidebar Styling:**
- Sidebar background: `#0f172a` (dark slate)
- Sidebar text (inactive): `#cbd5e1` (light slate)
- Sidebar text (active): `#60a5fa` (light blue)
- Active item background: `rgba(59, 130, 246, 0.1)` (semi-transparent blue)
- Active left border: `#3b82f6` (blue accent)
- Hover background: `rgba(255, 255, 255, 0.05)` (subtle white)
- Header border: `rgba(255, 255, 255, 0.1)` (subtle divider)

## Best Practices

### 1. Vue 3 Composition API Patterns

✅ **ALWAYS:**
- Use `<script setup>` syntax for components
- Keep components focused on single responsibility
- Use semantic HTML elements (`<aside>`, `<nav>`)
- Leverage Vue Router's automatic `.router-link-active` class

❌ **NEVER:**
- Mix Options API with Composition API
- Create overly complex components
- Hardcode navigation items (use router-link)

### 2. Preserve All Existing Functionality

✅ **MUST MAINTAIN:**
- All existing routes and their functionality
- Filter bar and filtering logic
- Modals (ProfileDetails, Tasks)
- Language switcher functionality
- Profile menu features
- Any existing state management

### 3. Consistent Spacing

**Sidebar:**
- Header padding: `1.5rem`
- Nav padding: `1rem 0` (top/bottom)
- Nav item padding: `0.75rem 1.5rem` (vertical, horizontal)

**Main Content:**
- Main content padding: `1.5rem 2rem`
- Card padding: `1.25rem`
- Grid gap: `1.25rem`

### 4. Router-Link Active State

✅ **GOOD:**
```vue
<router-link to="/" class="nav-item">Dashboard</router-link>

<style>
.nav-item.router-link-active {
  /* Active styles applied automatically by Vue Router */
  background: rgba(59, 130, 246, 0.1);
  color: #60a5fa;
  border-left-color: #3b82f6;
}
</style>
```

❌ **BAD:**
```vue
<!-- Don't manually manage active state -->
<a :class="{ active: isActive }" @click="navigate">Dashboard</a>
```

### 5. Z-Index Management

**Layer hierarchy (from highest to lowest):**
1. Modals: Keep existing z-index values (typically 1000+)
2. Sidebar: `z-index: 100`
3. Filter bar: `z-index: 90`
4. Main content: No z-index (natural stacking)

### 6. Accessibility

✅ **ENSURE:**
- Use semantic HTML (`<aside>`, `<nav>`)
- Maintain keyboard navigation
- Preserve focus states
- Keep color contrast ratios (WCAG AA minimum)

## Common Issues & Solutions

### Issue 1: FilterBar Overlaps with Sidebar

**Problem:** FilterBar positioning conflicts with sidebar

**Solution:**
```vue
<!-- Move FilterBar inside main-wrapper, not as a global fixed element -->
<div class="main-wrapper">
  <FilterBar />  <!-- Inside wrapper -->
  <main class="main-content">
    <router-view />
  </main>
</div>
```

### Issue 2: Main Content Too Wide

**Problem:** Content extends beyond desired width

**Solution:**
```css
.main-content {
  max-width: 1600px;
  margin: 0 auto;
  width: 100%;
}
```

### Issue 3: Sidebar Doesn't Scroll with Many Nav Items

**Problem:** Navigation items get cut off

**Solution:**
```css
.sidebar {
  overflow-y: auto; /* Enable vertical scrolling */
}
```

### Issue 4: Active Route Not Highlighting

**Problem:** Current route doesn't show active state

**Solution:**
- Vue Router automatically adds `.router-link-active` class
- Ensure CSS selector `.nav-item.router-link-active` exists
- Check that routes match exactly (trailing slashes)

### Issue 5: Content Jumps When Switching Routes

**Problem:** Layout shifts between page transitions

**Solution:**
```css
.main-wrapper {
  display: flex;
  flex-direction: column;
  min-height: 100vh; /* Prevent height changes */
}
```

### Issue 6: Sidebar Overlaps Modals

**Problem:** Z-index conflicts with modals

**Solution:**
```css
.sidebar { z-index: 100; }
.filter-bar { z-index: 90; }
/* Ensure modals have higher z-index (keep existing values, typically 1000+) */
```

## Mobile Responsiveness (Optional Enhancement)

While the primary focus is desktop, consider adding responsive behavior:

```css
@media (max-width: 768px) {
  .sidebar {
    transform: translateX(-100%);
    transition: transform 0.3s;
  }
  
  .sidebar.mobile-open {
    transform: translateX(0);
  }
  
  .main-wrapper {
    margin-left: 0;
  }
  
  /* Add hamburger menu button for mobile */
}
```

## Verification Steps

After implementation, systematically verify the following:

### Visual Checks

- [ ] Sidebar appears on left side (260px width)
- [ ] Sidebar has dark background (#0f172a)
- [ ] All 7 navigation routes listed (Dashboard, Inventory, Orders, Backlog, Finance, Demand, Reports)
- [ ] Logo/branding visible at top of sidebar
- [ ] Active route highlighted with blue accent and left border
- [ ] Main content area shifted right with proper spacing
- [ ] Filter bar positioned correctly (not overlapping sidebar)
- [ ] Consistent spacing throughout interface

### Functional Checks

- [ ] All routes navigate correctly
- [ ] Active route highlighting updates on navigation
- [ ] Hover states work on navigation items
- [ ] Filter bar remains functional with all filters working
- [ ] ProfileMenu accessible and functional
- [ ] TasksModal accessible and functional
- [ ] Language switcher still works (if applicable)
- [ ] Router transitions smooth with no layout shifts
- [ ] No content overlaps or visual glitches

### Browser Testing

- [ ] Test in Chrome
- [ ] Test in Edge
- [ ] Verify sidebar scroll behavior (if many nav items)
- [ ] Verify main content scroll behavior (independent from sidebar)
- [ ] Check hover states and transitions
- [ ] Verify no console errors

### Playwright Testing (Optional)

If you want automated testing, delegate to vue-expert with Playwright tools:

```javascript
// Navigate to each route
await page.goto('http://localhost:3000/')
await page.click('a[href="/inventory"]')
await page.click('a[href="/orders"]')
await page.click('a[href="/backlog"]')
await page.click('a[href="/spending"]')
await page.click('a[href="/demand"]')
await page.click('a[href="/reports"]')

// Verify sidebar visible on each page
const sidebar = await page.locator('.sidebar')
await expect(sidebar).toBeVisible()

// Verify active state changes
const activeLink = await page.locator('.nav-item.router-link-active')
await expect(activeLink).toHaveText('Orders')
```

## Quick Reference

**Create sidebar:** Delegate to vue-expert → `client/src/components/Sidebar.vue`

**Update layout:** Delegate to vue-expert → Modify `client/src/App.vue`

**Add route:** Delegate to vue-expert → Modify `client/src/main.js`

**Sidebar width:** 260px fixed

**Colors:** Dark sidebar (#0f172a), Light text (#cbd5e1), Active blue (#60a5fa)

**Spacing:** Header 1.5rem, Nav items 0.75rem/1.5rem, Main content 1.5rem/2rem

**Z-index:** Sidebar 100, Filter 90, Modals existing values

**Verify:** Visual checks, functional checks, browser testing

## Common Commands

```bash
# Start development servers
cd client && npm run dev      # Frontend on :3000
cd server && python main.py   # Backend on :8001

# Test in browser
open http://localhost:3000

# Run tests (if applicable)
npm run test
```

---

## Summary

This skill transforms horizontal navigation into a modern SaaS-style vertical sidebar:

1. **Create** Sidebar.vue component with navigation
2. **Update** App.vue layout (remove top nav, add sidebar + main-wrapper)
3. **Add** missing Backlog route
4. **Adjust** styles for proper positioning and spacing
5. **Verify** visual and functional correctness

**Remember:** All Vue file modifications MUST be delegated to vue-expert agent per CLAUDE.md mandatory rule.
