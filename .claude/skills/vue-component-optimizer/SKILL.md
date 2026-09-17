---
name: vue-component-optimizer
description: Analyze Vue 3 component structure and suggest optimizations for performance and code reuse. Use when reviewing components, refactoring, or optimizing application performance.
---

# Vue Component Optimizer

Comprehensive skill for analyzing Vue 3 components and providing actionable optimization recommendations for performance, maintainability, and code reuse.

## When to Use This Skill

Invoke this skill when:
- 🔍 Reviewing existing Vue components
- ⚡ Optimizing application performance
- ♻️ Refactoring for code reuse
- 🏗️ Conducting architecture reviews
- 🐛 Investigating performance issues
- 📦 Planning component extraction/composition

## Analysis Framework

### 1. Performance Analysis

#### A. Reactivity Optimization

**Check for:**
- ❌ **Unnecessary reactivity**: Using `ref()` for non-reactive data
- ❌ **Large reactive objects**: Entire API responses in refs
- ❌ **Reactive arrays without keys**: Missing `:key` in `v-for`
- ❌ **Computed property abuse**: Complex calculations on every render

**Good patterns:**
```vue
<!-- ✅ GOOD: Selective reactivity -->
<script setup>
import { ref, computed, shallowRef } from 'vue'

// Only make what changes reactive
const items = ref([])
const selectedId = ref(null)

// Use shallowRef for large objects that don't need deep reactivity
const largeData = shallowRef({ /* large object */ })

// Computed for derived state
const selectedItem = computed(() => 
  items.value.find(i => i.id === selectedId.value)
)
</script>
```

**Bad patterns:**
```vue
<!-- ❌ BAD: Over-reactivity -->
<script setup>
import { ref, reactive } from 'vue'

// Don't wrap everything
const apiResponse = reactive({ /* huge object */ })
const staticConfig = ref({ /* never changes */ })

// Inline complex calculations
const filtered = items.value.filter(/* complex */).map(/* complex */)
</script>
```

#### B. Computed Properties vs Methods

**Use computed for:**
- Derived state that depends on reactive data
- Values used in template multiple times
- Expensive calculations that should be cached

**Use methods for:**
- Event handlers
- Operations that don't depend on reactive data
- Side effects (API calls, mutations)

```vue
<script setup>
import { ref, computed } from 'vue'

const items = ref([])
const searchQuery = ref('')

// ✅ GOOD: Computed for filtering (cached)
const filteredItems = computed(() => 
  items.value.filter(item => 
    item.name.toLowerCase().includes(searchQuery.value.toLowerCase())
  )
)

// ✅ GOOD: Method for actions
const deleteItem = (id) => {
  items.value = items.value.filter(i => i.id !== id)
}

// ❌ BAD: Method for derived state
const getFilteredItems = () => {
  return items.value.filter(/* ... */) // Recalculates every render!
}
</script>

<template>
  <!-- Used multiple times - computed is perfect -->
  <div>{{ filteredItems.length }} items</div>
  <div v-for="item in filteredItems" :key="item.id">
    {{ item.name }}
    <button @click="deleteItem(item.id)">Delete</button>
  </div>
</template>
```

#### C. v-if vs v-show

**Use `v-if` when:**
- Condition rarely changes
- Component has expensive setup (watchers, lifecycle)
- Conditional rendering based on permissions/routes

**Use `v-show` when:**
- Toggling frequently (tabs, modals, filters)
- Component is lightweight
- Initial render cost is low

```vue
<template>
  <!-- ✅ v-show for frequent toggles -->
  <div v-show="isVisible" class="modal">
    Light content
  </div>

  <!-- ✅ v-if for expensive components -->
  <DataTable 
    v-if="shouldShowTable" 
    :data="largeDataset"
  />

  <!-- ✅ v-if for permissions -->
  <AdminPanel v-if="user.isAdmin" />
</template>
```

#### D. List Rendering Optimization

**Key requirements:**
- ✅ Always use unique, stable keys (IDs, not indexes)
- ✅ Avoid inline object creation in v-for
- ✅ Use `v-memo` for lists with expensive items (Vue 3.2+)

```vue
<template>
  <!-- ❌ BAD: Index as key -->
  <div v-for="(item, index) in items" :key="index">
    {{ item.name }}
  </div>

  <!-- ✅ GOOD: Stable unique key -->
  <div v-for="item in items" :key="item.id">
    {{ item.name }}
  </div>

  <!-- ✅ EXCELLENT: v-memo for expensive items (Vue 3.2+) -->
  <div 
    v-for="item in items" 
    :key="item.id"
    v-memo="[item.id, item.name, item.price]"
  >
    <ExpensiveComponent :item="item" />
  </div>

  <!-- ❌ BAD: Inline object creation -->
  <ChildComponent 
    v-for="item in items"
    :key="item.id"
    :config="{ id: item.id, name: item.name }"
  />

  <!-- ✅ GOOD: Computed or pre-processed -->
  <ChildComponent 
    v-for="item in processedItems"
    :key="item.id"
    :config="item.config"
  />
</template>
```

### 2. Code Reuse Analysis

#### A. Composable Extraction

**Extract to composables when you see:**
- Duplicated reactive state logic
- Shared data fetching patterns
- Reusable stateful behaviors

**Composable patterns:**

```javascript
// ✅ GOOD: Reusable data fetching
// composables/useDataFetch.js
import { ref, watch } from 'vue'

export function useDataFetch(fetchFn) {
  const data = ref(null)
  const loading = ref(false)
  const error = ref(null)

  const execute = async (...args) => {
    loading.value = true
    error.value = null
    try {
      data.value = await fetchFn(...args)
    } catch (e) {
      error.value = e
    } finally {
      loading.value = false
    }
  }

  return { data, loading, error, execute, refetch: execute }
}

// Usage in components:
const { data: orders, loading, execute: fetchOrders } = useDataFetch(api.getOrders)
const { data: inventory, loading: invLoading, execute: fetchInventory } = useDataFetch(api.getInventory)
```

**Common composable candidates:**
- `useFilters()` - Filter state and logic
- `usePagination()` - Pagination state
- `useSort()` - Sorting logic
- `useModal()` - Modal open/close state
- `useAuth()` - Authentication state
- `useLocalStorage()` - Persistent state

#### B. Component Composition

**Extract components when:**
- UI pattern repeats 3+ times
- Section has independent state/logic
- Component becomes > 300 lines

**Example: Card pattern**

```vue
<!-- ❌ BAD: Repeated card structure -->
<template>
  <div class="card">
    <div class="card-header">Orders</div>
    <div class="card-body">{{ orders.length }}</div>
  </div>
  
  <div class="card">
    <div class="card-header">Revenue</div>
    <div class="card-body">${{ revenue }}</div>
  </div>
  
  <div class="card">
    <div class="card-header">Inventory</div>
    <div class="card-body">{{ inventory.length }}</div>
  </div>
</template>

<!-- ✅ GOOD: Extracted component -->
<!-- components/MetricCard.vue -->
<template>
  <div class="card">
    <div class="card-header">
      <slot name="title">{{ title }}</slot>
    </div>
    <div class="card-body">
      <slot>{{ value }}</slot>
    </div>
  </div>
</template>

<script setup>
defineProps({
  title: String,
  value: [String, Number]
})
</script>

<!-- Usage: -->
<template>
  <MetricCard title="Orders" :value="orders.length" />
  <MetricCard title="Revenue" :value="`$${revenue}`" />
  <MetricCard title="Inventory" :value="inventory.length" />
</template>
```

#### C. Props vs Slots

**Use props when:**
- Passing simple data
- Parent controls child behavior
- Type validation needed

**Use slots when:**
- Custom content/layout needed
- Multiple content sections
- Flexibility is priority

```vue
<!-- ✅ GOOD: Flexible component with slots -->
<template>
  <div class="table-wrapper">
    <div class="table-header">
      <slot name="header">
        <h3>{{ title }}</h3>
      </slot>
    </div>
    
    <table>
      <thead>
        <slot name="columns" />
      </thead>
      <tbody>
        <slot name="rows" />
      </tbody>
    </table>
    
    <div class="table-footer">
      <slot name="footer">
        <div>{{ items.length }} items</div>
      </slot>
    </div>
  </div>
</template>
```

### 3. Template Optimization

#### A. Avoid Inline Expressions

```vue
<!-- ❌ BAD: Complex inline logic -->
<template>
  <div v-if="user && user.role === 'admin' && user.permissions.includes('write')">
    Admin content
  </div>
  
  <div>
    {{ items.filter(i => i.active).map(i => i.name).join(', ') }}
  </div>
</template>

<!-- ✅ GOOD: Move to computed -->
<script setup>
const isAdmin = computed(() => 
  user.value?.role === 'admin' && 
  user.value?.permissions.includes('write')
)

const activeItemNames = computed(() =>
  items.value
    .filter(i => i.active)
    .map(i => i.name)
    .join(', ')
)
</script>

<template>
  <div v-if="isAdmin">Admin content</div>
  <div>{{ activeItemNames }}</div>
</template>
```

#### B. Event Handler Optimization

```vue
<!-- ❌ BAD: Inline arrow functions -->
<template>
  <button 
    v-for="item in items" 
    :key="item.id"
    @click="() => selectItem(item.id)"
  >
    {{ item.name }}
  </button>
</template>

<!-- ✅ GOOD: Direct method reference or event argument -->
<template>
  <button 
    v-for="item in items" 
    :key="item.id"
    @click="selectItem(item.id)"
  >
    {{ item.name }}
  </button>
</template>

<script setup>
// Method accepts id directly
const selectItem = (id) => {
  selectedId.value = id
}
</script>
```

### 4. State Management Patterns

#### A. Props Drilling

**Problem:** Passing props through multiple levels

```vue
<!-- ❌ BAD: Props drilling -->
<!-- Grandparent.vue -->
<Parent :user="user" :theme="theme" :config="config" />

<!-- Parent.vue -->
<Child :user="user" :theme="theme" :config="config" />

<!-- Child.vue -->
<GrandChild :user="user" :theme="theme" />
```

**Solutions:**

1. **Provide/Inject** (for component trees)
```vue
<!-- ✅ GOOD: Provide/Inject -->
<!-- Grandparent.vue -->
<script setup>
import { provide } from 'vue'

provide('user', user)
provide('theme', theme)
</script>

<!-- GrandChild.vue -->
<script setup>
import { inject } from 'vue'

const user = inject('user')
const theme = inject('theme')
</script>
```

2. **Composables** (for shared state)
```javascript
// composables/useAppState.js
import { reactive } from 'vue'

const appState = reactive({
  user: null,
  theme: 'light',
  config: {}
})

export function useAppState() {
  return appState
}

// Any component:
const { user, theme } = useAppState()
```

#### B. Local vs Shared State

**Keep local:**
- UI state (open/closed, selected)
- Form input values (before submit)
- Component-specific filters

**Make shared:**
- User authentication state
- Application settings
- API data cache

```vue
<!-- ✅ GOOD: Clear separation -->
<script setup>
// Local UI state
const isOpen = ref(false)
const searchQuery = ref('')

// Shared state via composable
const { user, logout } = useAuth()
const { orders, fetchOrders } = useOrders()
</script>
```

### 5. Performance Monitoring Patterns

#### A. Add Performance Tracking

```vue
<script setup>
import { onMounted, onUpdated } from 'vue'

// Development-only performance tracking
if (import.meta.env.DEV) {
  onMounted(() => {
    console.time('ComponentMount')
  })
  
  onUpdated(() => {
    console.timeEnd('ComponentMount')
  })
}
</script>
```

#### B. Lazy Loading

```vue
<!-- ✅ GOOD: Lazy load heavy components -->
<script setup>
import { defineAsyncComponent } from 'vue'

const HeavyChart = defineAsyncComponent(() =>
  import('./components/HeavyChart.vue')
)

const AdminPanel = defineAsyncComponent(() =>
  import('./components/AdminPanel.vue')
)
</script>

<template>
  <div v-if="showChart">
    <Suspense>
      <template #default>
        <HeavyChart :data="chartData" />
      </template>
      <template #fallback>
        <div>Loading chart...</div>
      </template>
    </Suspense>
  </div>
</template>
```

### 6. Common Anti-Patterns

#### ❌ Anti-Pattern Checklist

- [ ] Mutating props directly
- [ ] Using `$refs` for data flow
- [ ] Excessive `watch()` instead of `computed()`
- [ ] Not cleaning up event listeners in `onUnmounted()`
- [ ] Accessing `$parent` or `$root`
- [ ] Global state in component files
- [ ] Computed properties with side effects
- [ ] Using `any` type in TypeScript props

## Analysis Workflow

### Step 1: Component Scan

```bash
# Find all Vue components
find client/src -name "*.vue" -type f
```

### Step 2: Analyze Structure

For each component, check:
1. **Lines of code** - Should be < 300 lines
2. **Script setup usage** - Modern Composition API
3. **Number of props** - Should be < 10
4. **Computed properties** - Are they necessary?
5. **Watchers** - Can they be computed instead?
6. **Template complexity** - Deep nesting?

### Step 3: Extract Metrics

```vue
<script setup>
// Metrics to track:
// - Number of reactive refs
// - Number of computed properties
// - Number of watchers
// - Number of lifecycle hooks
// - Template depth
// - Number of event handlers
</script>
```

### Step 4: Generate Report

**Report template:**

```markdown
## Component Analysis: [ComponentName.vue]

### Performance Score: [8/10]

#### Strengths:
- ✅ Uses Composition API
- ✅ Proper key usage in v-for
- ✅ Computed properties for derived state

#### Issues Found:
- ⚠️ Large reactive object (200+ keys)
- ⚠️ Missing v-memo on expensive list items
- ⚠️ Inline arrow functions in template

#### Recommendations:
1. **Extract composable**: Lines 45-89 contain reusable filter logic
2. **Use shallowRef**: `apiResponse` doesn't need deep reactivity
3. **Add v-memo**: List items at line 120-145 render expensively
4. **Split component**: Component is 450 lines, consider extracting sections

#### Refactoring Priority: High
```

## Optimization Checklist

### Performance
- [ ] All v-for loops have unique :key
- [ ] Computed properties used for derived state
- [ ] v-show for frequent toggles
- [ ] v-if for expensive components
- [ ] No inline arrow functions in templates
- [ ] Large lists use v-memo (Vue 3.2+)
- [ ] Heavy components are lazy-loaded

### Code Reuse
- [ ] Duplicate logic extracted to composables
- [ ] Repeated UI patterns extracted to components
- [ ] Props drilling avoided (use provide/inject or composables)
- [ ] Shared state in composables, not duplicated

### Maintainability
- [ ] Component < 300 lines
- [ ] Clear prop types defined
- [ ] Slots used for flexible content
- [ ] Meaningful variable names
- [ ] Complex logic documented

### Best Practices
- [ ] Using `<script setup>`
- [ ] Not mutating props
- [ ] Cleaning up side effects (listeners, intervals)
- [ ] TypeScript types for props (if using TS)
- [ ] Accessibility attributes (aria-*)

## Quick Wins

### 1. Convert to Composition API
```bash
# Find Options API components
grep -l "export default {" client/src/**/*.vue
```

### 2. Extract Repeated Code
```bash
# Find duplicate filter logic
grep -r "filter(" client/src/views/*.vue
```

### 3. Add Keys to Lists
```bash
# Find v-for without :key
grep -n "v-for" client/src/**/*.vue | grep -v ":key"
```

## Resources

- [Vue 3 Performance Guide](https://vuejs.org/guide/best-practices/performance.html)
- [Composition API FAQ](https://vuejs.org/guide/extras/composition-api-faq.html)
- [Vue Composables](https://vueuse.org/)

---

## Usage Example

Invoke this skill:
```
Use the vue-component-optimizer skill to analyze Dashboard.vue and suggest performance improvements
```

Or for bulk analysis:
```
Analyze all components in client/src/views/ for optimization opportunities
```
