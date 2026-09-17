# Vue Component Optimizer Skill

AI-powered skill for analyzing Vue 3 components and suggesting performance and code reuse optimizations.

## Quick Start

### Analyze a Single Component
```
Use the vue-component-optimizer skill to analyze client/src/views/Dashboard.vue
```

### Analyze Multiple Components
```
Use the vue-component-optimizer skill to review all view components in client/src/views/
```

### Performance Audit
```
Run a performance audit on the inventory management system using vue-component-optimizer
```

## What It Checks

### ⚡ Performance
- Reactivity optimization (ref vs shallowRef)
- Computed vs methods usage
- v-if vs v-show decisions
- List rendering (keys, v-memo)
- Template complexity
- Event handler optimization

### ♻️ Code Reuse
- Composable extraction opportunities
- Component composition patterns
- Props vs slots decisions
- State management (local vs shared)
- Props drilling issues

### 🏗️ Architecture
- Component size (< 300 lines)
- Separation of concerns
- Provide/Inject usage
- Lazy loading opportunities

### 🐛 Anti-Patterns
- Props mutation
- $refs for data flow
- Excessive watchers
- Missing cleanup
- Complex inline expressions

## Example Reports

### Good Component
```
✅ Dashboard.vue - Score: 9/10
- Uses Composition API
- Proper reactivity
- Well-structured
- Minor: Could extract filter logic to composable
```

### Needs Work
```
⚠️ Orders.vue - Score: 5/10
Issues:
1. 450 lines (split recommended)
2. Missing :key in v-for (line 89)
3. Inline arrow functions in template
4. Large reactive object (consider shallowRef)

Recommendations:
- Extract OrderFilters composable
- Split into OrderList + OrderDetails
- Add v-memo for list items
```

## Integration with Project

This skill is specifically designed for the Inventory Management System:
- **Target**: Vue 3 + Composition API
- **Focus**: `client/src/` directory
- **Views**: Dashboard, Inventory, Orders, Spending, Demand, Reports, Backlog
- **Components**: Sidebar, FilterBar, Modals

## Best Practices Enforced

1. **Always** use `<script setup>`
2. **Always** use unique :key in v-for
3. **Prefer** computed over methods for derived state
4. **Prefer** composables over mixins
5. **Limit** components to < 300 lines
6. **Extract** repeated logic to composables
7. **Use** v-memo for expensive list items
8. **Lazy load** heavy components

## When to Use

- ✅ Before submitting PR (component review)
- ✅ After adding new features (optimization check)
- ✅ When experiencing performance issues
- ✅ During refactoring sessions
- ✅ For code review preparation

## Output Format

The skill provides:
1. **Performance Score** (1-10)
2. **Strengths** (what's done well)
3. **Issues** (problems found with line numbers)
4. **Recommendations** (actionable improvements)
5. **Priority** (Low/Medium/High/Critical)

## Related Skills

- `vue-expert` - For Vue component creation/modification
- `saas-ui-redesign` - For UI/UX improvements
- `code-reviewer` - For general code quality

## Version

- **Created**: 2026-09-17
- **Target**: Vue 3.4+, Composition API
- **Compatibility**: Vite, vue-router, axios

## Maintenance

Update this skill when:
- New Vue features are released
- Performance best practices change
- Project patterns evolve
- New anti-patterns discovered
