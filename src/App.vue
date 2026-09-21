<template>
  <div id="app">
    <navbar/>
    <router-view />
  </div>
</template>

<script>
  import navbar from "./components/navbar.vue"
  
  export default {
    name: "root",
  
    components: {
      navbar,
    }
  }
  </script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&family=Poppins:wght@400;500;600;700&display=swap');

:root {
  --font-main: 'Plus Jakarta Sans', 'Poppins', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  
  /* Backgrounds */
  --bg-main: #0b0f19;
  --bg-surface: #111827;
  --bg-card: rgba(17, 24, 39, 0.75);
  --bg-card-hover: rgba(30, 41, 59, 0.85);
  --bg-input: rgba(15, 23, 42, 0.65);
  
  /* Glassmorphism & Borders */
  --border-glass: rgba(255, 255, 255, 0.08);
  --border-glass-hover: rgba(255, 255, 255, 0.16);
  --border-focus: #6366f1;
  --backdrop-blur: blur(16px);
  
  /* Text */
  --text-primary: #f8fafc;
  --text-secondary: #cbd5e1;
  --text-muted: #94a3b8;
  
  /* Accents & Gradients */
  --accent-primary: #6366f1;
  --accent-primary-hover: #4f46e5;
  --accent-secondary: #ec4899;
  --accent-gradient: linear-gradient(135deg, #6366f1 0%, #a855f7 50%, #ec4899 100%);
  --accent-gradient-hover: linear-gradient(135deg, #4f46e5 0%, #9333ea 50%, #db2777 100%);
  --danger-color: #ef4444;
  --danger-bg: rgba(239, 68, 68, 0.15);
  --danger-border: rgba(239, 68, 68, 0.3);
  --danger-hover: #dc2626;
  
  /* Shadows */
  --glass-shadow: 0 10px 30px -10px rgba(0, 0, 0, 0.5), 0 0 1px 1px rgba(255, 255, 255, 0.05);
  --glow-shadow: 0 0 25px rgba(99, 102, 241, 0.35);
}

* {
  box-sizing: border-box;
}

body {
  margin: 0;
  min-height: 100vh;
  background-color: var(--bg-main) !important;
  color: var(--text-primary);
  font-family: var(--font-main);
  background-image: 
    radial-gradient(circle at 15% 15%, rgba(99, 102, 241, 0.15) 0%, transparent 40%),
    radial-gradient(circle at 85% 85%, rgba(236, 72, 153, 0.12) 0%, transparent 40%);
  background-attachment: fixed;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

#app {
  font-family: var(--font-main);
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

/* Custom Scrollbar */
::-webkit-scrollbar {
  width: 8px;
}
::-webkit-scrollbar-track {
  background: var(--bg-main);
}
::-webkit-scrollbar-thumb {
  background: rgba(255, 255, 255, 0.15);
  border-radius: 4px;
}
::-webkit-scrollbar-thumb:hover {
  background: rgba(255, 255, 255, 0.25);
}
</style>
