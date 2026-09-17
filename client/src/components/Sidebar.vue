<template>
  <aside class="sidebar" :class="{ collapsed: isCollapsed }">
    <div class="sidebar-header">
      <h1 class="logo" v-show="!isCollapsed">{{ t('nav.companyName') }}</h1>
      <span class="subtitle" v-show="!isCollapsed">{{ t('nav.subtitle') }}</span>
      <div class="logo-icon" v-show="isCollapsed">🏭</div>
    </div>

    <nav class="sidebar-nav">
      <router-link to="/" class="nav-item" :title="t('nav.overview')">
        <span class="nav-icon">📊</span>
        <span class="nav-label" v-show="!isCollapsed">{{ t('nav.overview') }}</span>
      </router-link>
      <router-link to="/inventory" class="nav-item" :title="t('nav.inventory')">
        <span class="nav-icon">📦</span>
        <span class="nav-label" v-show="!isCollapsed">{{ t('nav.inventory') }}</span>
      </router-link>
      <router-link to="/orders" class="nav-item" :title="t('nav.orders')">
        <span class="nav-icon">📋</span>
        <span class="nav-label" v-show="!isCollapsed">{{ t('nav.orders') }}</span>
      </router-link>
      <router-link to="/backlog" class="nav-item" title="Backlog">
        <span class="nav-icon">⚠️</span>
        <span class="nav-label" v-show="!isCollapsed">Backlog</span>
      </router-link>
      <router-link to="/spending" class="nav-item" :title="t('nav.finance')">
        <span class="nav-icon">💰</span>
        <span class="nav-label" v-show="!isCollapsed">{{ t('nav.finance') }}</span>
      </router-link>
      <router-link to="/demand" class="nav-item" :title="t('nav.demandForecast')">
        <span class="nav-icon">📈</span>
        <span class="nav-label" v-show="!isCollapsed">{{ t('nav.demandForecast') }}</span>
      </router-link>
      <router-link to="/reports" class="nav-item" title="Reports">
        <span class="nav-icon">📄</span>
        <span class="nav-label" v-show="!isCollapsed">Reports</span>
      </router-link>
    </nav>

    <button class="toggle-btn" @click="toggleSidebar" :title="isCollapsed ? 'Expand sidebar' : 'Collapse sidebar'">
      <span v-if="isCollapsed">→</span>
      <span v-else>←</span>
    </button>
  </aside>
</template>

<script setup>
import { ref } from 'vue'
import { useI18n } from '../composables/useI18n'

const { t } = useI18n()
const isCollapsed = ref(false)

const toggleSidebar = () => {
  isCollapsed.value = !isCollapsed.value
}
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
  transition: width 0.3s ease;
}

.sidebar.collapsed {
  width: 70px;
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

.logo-icon {
  font-size: 2rem;
  text-align: center;
}

.sidebar-nav {
  flex: 1;
  padding: 1rem 0;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
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

.nav-icon {
  font-size: 1.25rem;
  flex-shrink: 0;
  width: 1.5rem;
  text-align: center;
}

.nav-label {
  font-size: 0.938rem;
  font-weight: 500;
  white-space: nowrap;
  overflow: hidden;
}

.toggle-btn {
  position: absolute;
  bottom: 1rem;
  right: 1rem;
  background: rgba(255, 255, 255, 0.1);
  border: 1px solid rgba(255, 255, 255, 0.2);
  color: white;
  width: 2rem;
  height: 2rem;
  border-radius: 4px;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
  font-size: 1rem;
}

.toggle-btn:hover {
  background: rgba(255, 255, 255, 0.2);
}

.sidebar.collapsed .toggle-btn {
  right: 50%;
  transform: translateX(50%);
}

@media (max-width: 1024px) {
  .sidebar {
    width: 70px;
  }

  .nav-label,
  .logo,
  .subtitle {
    display: none !important;
  }

  .logo-icon {
    display: block !important;
  }
}
</style>
