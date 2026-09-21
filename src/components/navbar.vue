<template>
  <b-navbar toggleable="lg" class="custom-navbar" type="dark">
    <div class="container-fluid px-lg-4">
      <b-navbar-brand class="brand-title" to="/">
        <span class="brand-icon">✨</span>
        <span class="brand-text">TaskFlow</span>
      </b-navbar-brand>

      <b-navbar-toggle target="nav-collapse" class="border-0 shadow-none"></b-navbar-toggle>

      <b-collapse id="nav-collapse" is-nav>
        <b-navbar-nav class="ms-auto align-items-center">
          <b-nav-item to="/" class="nav-link-custom" active-class="active-link" exact>
            <span class="nav-icon">📊</span> Dashboard
          </b-nav-item>
          <b-nav-item to="/form" class="nav-link-custom nav-btn-highlight" active-class="active-link" exact>
            <span class="nav-icon">➕</span> Thêm công việc
          </b-nav-item>
        </b-navbar-nav>
      </b-collapse>
    </div>
  </b-navbar>
</template>

<script>
export default {
  name: "Navbar",
}
</script>

<style scoped>
.custom-navbar {
  background: rgba(11, 15, 25, 0.75) !important;
  backdrop-filter: var(--backdrop-blur);
  -webkit-backdrop-filter: var(--backdrop-blur);
  border-bottom: 1px solid var(--border-glass);
  box-shadow: 0 4px 20px 0 rgba(0, 0, 0, 0.35);
  padding: 0.85rem 1rem;
  position: sticky;
  top: 0;
  z-index: 1000;
  transition: all 0.3s ease;
}

.brand-title {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  text-decoration: none;
}

.brand-icon {
  font-size: 1.25rem;
  filter: drop-shadow(0 0 8px rgba(99, 102, 241, 0.6));
}

.brand-text {
  font-weight: 800;
  font-size: 1.35rem;
  letter-spacing: -0.5px;
  background: var(--accent-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  transition: opacity 0.2s ease;
}

.brand-title:hover .brand-text {
  opacity: 0.9;
}

:deep(.nav-link-custom .nav-link) {
  color: var(--text-secondary) !important;
  font-weight: 500;
  font-size: 0.95rem;
  padding: 0.5rem 1rem !important;
  border-radius: 10px;
  transition: all 0.2s ease;
  display: flex;
  align-items: center;
  gap: 0.4rem;
  margin: 0.2rem 0.3rem;
}

:deep(.nav-link-custom .nav-link:hover) {
  color: var(--text-primary) !important;
  background: rgba(255, 255, 255, 0.06);
  transform: translateY(-1px);
}

:deep(.nav-link-custom.active-link .nav-link),
:deep(.nav-link-custom .nav-link.active) {
  color: #fff !important;
  background: rgba(99, 102, 241, 0.15) !important;
  border: 1px solid rgba(99, 102, 241, 0.3);
  box-shadow: 0 0 15px rgba(99, 102, 241, 0.2);
}

:deep(.nav-btn-highlight .nav-link) {
  background: rgba(99, 102, 241, 0.1);
  border: 1px solid rgba(99, 102, 241, 0.25);
  color: #c7d2fe !important;
}

:deep(.nav-btn-highlight .nav-link:hover) {
  background: var(--accent-gradient);
  color: #ffffff !important;
  box-shadow: var(--glow-shadow);
  border-color: transparent;
}
</style>