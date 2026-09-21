<template>
  <div class="home-wrapper">
    <div class="hero-card">
      <div class="badge-pill">
        <span class="badge-dot"></span>
        <span>Quản lý công việc thông minh & tinh tế</span>
      </div>

      <h1 class="hero-title">
        Làm chủ thời gian với <span class="gradient-text">TaskFlow</span>
      </h1>

      <p class="hero-desc">
        Tổ chức, theo dõi và hoàn thành mọi mục tiêu hàng ngày với giao diện Dark Mode trực quan, mượt mà và tối giản.
      </p>

      <div class="cta-group">
        <b-button to="/list" class="btn-primary-gradient">
          <span>📋 Xem danh sách việc</span>
        </b-button>
        <b-button to="/form" class="btn-glass">
          <span>➕ Thêm việc mới</span>
        </b-button>
      </div>

      <div class="features-row">
        <div class="feature-item">
          <span class="feature-icon">⚡</span>
          <span class="feature-text">Nhanh chóng & Mượt mà</span>
        </div>
        <div class="feature-item">
          <span class="feature-icon">🔒</span>
          <span class="feature-text">Lưu trữ cục bộ an toàn</span>
        </div>
        <div class="feature-item">
          <span class="feature-icon">🎨</span>
          <span class="feature-text">Giao diện Modern Slate</span>
        </div>
      </div>

   
    </div>
  </div>
</template>

<script>
export default {
  name: "Home",
}
</script>

<style scoped>
.home-wrapper {
  flex: 1;
  min-height: calc(100vh - 75px);
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 2rem 1.5rem;
}

.hero-card {
  max-width: 820px;
  width: 100%;
  background: var(--bg-card);
  backdrop-filter: var(--backdrop-blur);
  -webkit-backdrop-filter: var(--backdrop-blur);
  border: 1px solid var(--border-glass);
  border-radius: 28px;
  padding: 3.5rem 2.5rem;
  box-shadow: var(--glass-shadow);
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  animation: fadeIn 0.8s ease-out;
}

@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(16px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.badge-pill {
  display: inline-flex;
  align-items: center;
  gap: 0.6rem;
  padding: 0.45rem 1.15rem;
  background: rgba(99, 102, 241, 0.12);
  border: 1px solid rgba(99, 102, 241, 0.3);
  border-radius: 9999px;
  color: #c7d2fe;
  font-size: 0.875rem;
  font-weight: 500;
  margin-bottom: 1.75rem;
}

.badge-dot {
  width: 8px;
  height: 8px;
  background-color: #6366f1;
  border-radius: 50%;
  box-shadow: 0 0 10px #6366f1;
}

.hero-title {
  font-size: clamp(2.4rem, 5vw, 3.8rem);
  font-weight: 800;
  color: var(--text-primary);
  line-height: 1.15;
  letter-spacing: -1px;
  margin-bottom: 1.25rem;
}

.gradient-text {
  background: var(--accent-gradient);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  display: inline-block;
}

.hero-desc {
  font-size: clamp(1rem, 2vw, 1.2rem);
  color: var(--text-muted);
  max-width: 600px;
  line-height: 1.6;
  margin-bottom: 2.25rem;
}

.cta-group {
  display: flex;
  gap: 1rem;
  flex-wrap: wrap;
  justify-content: center;
  margin-bottom: 2.75rem;
}

.btn-primary-gradient {
  background: var(--accent-gradient) !important;
  border: none !important;
  color: #ffffff !important;
  font-weight: 600;
  font-size: 1.05rem;
  padding: 0.8rem 1.75rem;
  border-radius: 14px;
  box-shadow: 0 4px 20px rgba(99, 102, 241, 0.4);
  transition: all 0.25s ease;
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
}

.btn-primary-gradient:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 25px rgba(99, 102, 241, 0.6);
}

.btn-glass {
  background: rgba(255, 255, 255, 0.05) !important;
  border: 1px solid var(--border-glass-hover) !important;
  color: var(--text-primary) !important;
  font-weight: 600;
  font-size: 1.05rem;
  padding: 0.8rem 1.75rem;
  border-radius: 14px;
  backdrop-filter: blur(8px);
  transition: all 0.25s ease;
  display: inline-flex;
  align-items: center;
  gap: 0.5rem;
}

.btn-glass:hover {
  background: rgba(255, 255, 255, 0.12) !important;
  color: #ffffff !important;
  transform: translateY(-2px);
  border-color: rgba(255, 255, 255, 0.25) !important;
}

.features-row {
  display: flex;
  gap: 1.5rem;
  flex-wrap: wrap;
  justify-content: center;
  padding-top: 1.75rem;
  border-top: 1px solid var(--border-glass);
  width: 100%;
}

.feature-item {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  color: var(--text-secondary);
  font-size: 0.9rem;
  font-weight: 500;
}

.feature-icon {
  font-size: 1.1rem;
}

.footer-credit {
  margin-top: 2rem;
  font-size: 0.875rem;
  color: var(--text-muted);
}

.footer-credit a {
  color: #a855f7;
  text-decoration: none;
  font-weight: 600;
  transition: color 0.2s;
}

.footer-credit a:hover {
  color: #ec4899;
  text-decoration: underline;
}

@media (max-width: 576px) {
  .hero-card {
    padding: 2.25rem 1.25rem;
    border-radius: 20px;
  }
  .cta-group {
    flex-direction: column;
    width: 100%;
  }
  .btn-primary-gradient, .btn-glass {
    width: 100%;
    justify-content: center;
  }
  .features-row {
    flex-direction: column;
    gap: 0.75rem;
    align-items: center;
  }
}
</style>
