<script setup lang="ts">
import { useRouter } from 'vue-router'

const router = useRouter()

const handleLogout = () => {
  // Clear all authentication data
  localStorage.removeItem('authToken')
  localStorage.removeItem('userEmail')
  localStorage.removeItem('rememberMe')

  sessionStorage.removeItem('authToken')
  sessionStorage.removeItem('userEmail')

  router.push('/')
}
</script>

<template>
  <div class="retro-container crt-screen retro-dashboard">
    <!-- CRT Screen Effect -->
    <div class="crt-overlay"></div>
    <div class="scanlines"></div>

    <header class="retro-header">
      <div class="pixel-border">
        <div class="header-content">
          <div class="system-info">█ SYSTEM ACCESS GRANTED █</div>
          <button @click="handleLogout" class="retro-logout">
            <span class="button-text">◀ LOGOUT</span>
          </button>
        </div>
      </div>
    </header>

    <main class="retro-main">
      <div class="retro-content">

        <div class="welcome-section">
          <div class="kitty-display-large">
            <div class="image-border-large">
              <img src="@/assets/kitty.png" alt="Kitty" class="retro-kitty-large" />
            </div>
          </div>

          <div class="message-display-compact">
            <div class="retro-message-simple">
              <h1 class="retro-greeting">GRETTINGS BUDDY, YOU IN!</h1>
            </div>
          </div>
        </div>

        <div class="retro-decorations">
          <div class="corner-decoration top-left">█</div>
          <div class="corner-decoration top-right">█</div>
          <div class="corner-decoration bottom-left">█</div>
          <div class="corner-decoration bottom-right">█</div>
        </div>
      </div>
    </main>
  </div>
</template>

<style scoped lang="scss">
@use '../assets/variables' as *;

.retro-dashboard {
  position: relative;
  min-height: 100vh;
  overflow: hidden;
}

.retro-header {
  padding: $retro-spacing-md $retro-spacing-xl;
  position: relative;
  z-index: 2;
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: $retro-spacing-sm;
}

.system-info {
  font-family: $retro-font-family;
  font-size: $retro-font-size-lg;
  color: $retro-yellow;
  text-shadow: 0 0 8px $retro-yellow;
  letter-spacing: 1px;
  font-weight: bold;
}

.retro-logout {
  padding: $retro-spacing-sm $retro-spacing-md;
  background: #602020;
  border: $retro-border-thin solid $retro-red-dark;
  border-radius: 0;
  color: #d4a4a4;
  font-family: $retro-font-family;
  font-size: $retro-font-size-md;
  font-weight: bold;
  cursor: pointer;
  text-transform: uppercase;
  letter-spacing: 1px;
  box-shadow: $retro-shadow-sm $retro-glow-red;
  transition: all $retro-transition-base;

  &:hover {
    background: #702828;
    border-color: #bf6262;
    box-shadow: 0 0 12px rgba(191, 98, 98, 0.5);
    transform: scale(1.05);
  }

  &:active {
    transform: scale(0.95);
  }

  &:focus-visible {
    outline: 3px solid $retro-yellow;
    outline-offset: 2px;
  }
}

.retro-main {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: calc(100vh - 100px);
  padding: $retro-spacing-xl;
  position: relative;
  z-index: 1;
}

.retro-content {
  position: relative;
  background: $retro-bg-secondary;
  border: $retro-border-medium solid $retro-border-primary;
  padding: $retro-spacing-xl;
  box-shadow:
    $retro-shadow-md $retro-glow-green,
    inset $retro-shadow-md rgba(74, 159, 74, 0.05);
  z-index: 1;
  width: 100%;
  max-width: 800px;
}

.welcome-section {
  display: grid;
  grid-template-columns: 70% 30%;
  gap: $retro-spacing-xl;
  align-items: center;
  height: 100%;
}

.kitty-display-large {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100%;
}

.image-border-large {
  border: $retro-border-thick solid $retro-yellow;
  padding: $retro-spacing-md;
  background: $retro-bg-tertiary;
  box-shadow: $retro-shadow-lg rgba(168, 168, 50, 0.4);
  border-radius: 0;
}

.retro-kitty-large {
  width: 300px;
  height: 300px;
  object-fit: cover;
  border: $retro-border-medium solid $retro-border-primary;
  background: $retro-bg-primary;
  image-rendering: pixelated;
  border-radius: 0;
}

.message-display-compact {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100%;
}

.retro-message-simple {
  text-align: center;
  padding: $retro-spacing-md;
}

.retro-greeting {
  font-family: $retro-font-family;
  font-size: 2.5rem;
  color: $retro-yellow;
  text-shadow: 0 0 12px $retro-yellow;
  font-weight: bold;
  letter-spacing: 3px;
  text-transform: uppercase;
  line-height: 1.2;
  margin: 0;
}

.retro-decorations {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  pointer-events: none;
}

@media (max-width: 768px) {
  .retro-main {
    padding: $retro-spacing-md;
  }

  .retro-content {
    padding: $retro-spacing-lg;
  }

  .welcome-section {
    grid-template-columns: 1fr;
    gap: $retro-spacing-lg;
  }

  .retro-kitty-large {
    width: 200px;
    height: 200px;
  }

  .retro-greeting {
    font-size: $retro-font-size-2xl;
  }

  .header-content {
    flex-direction: column;
    gap: $retro-spacing-md;
    text-align: center;
  }

  .system-info {
    font-size: $retro-font-size-base;
  }
}
</style>