<script setup lang="ts">
import { useRouter } from 'vue-router'

const router = useRouter()

const handleLogout = () => {
  router.push('/')
}
</script>

<template>
  <div class="retro-dashboard">
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
.retro-dashboard {
  position: relative;
  min-height: 100vh;
  background: #1a1a1a;
  overflow: hidden;

  // CRT Screen Effect
  &::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: rgba(0, 80, 0, 0.05);
    pointer-events: none;
  }
}

.crt-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 60, 0, 0.02);
  pointer-events: none;
  animation: flicker 0.15s infinite linear alternate;
}

.scanlines {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 2px,
    rgba(0, 40, 0, 0.02) 2px,
    rgba(0, 40, 0, 0.02) 4px
  );
  pointer-events: none;
}

.retro-header {
  padding: 1rem 2rem;
  position: relative;
  z-index: 2;
}

.pixel-border {
  border: 2px solid #4a9f4a;
  background: #2d2d2d;
  box-shadow: 0 0 8px rgba(74, 159, 74, 0.2);
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.5rem;
}

.system-info {
  font-family: 'Courier New', monospace;
  font-size: 1.2rem;
  color: #a8a832;
  text-shadow: 0 0 8px #a8a832;
  letter-spacing: 1px;
  font-weight: bold;
}

.retro-logout {
  padding: 0.5rem 1rem;
  background: #602020;
  border: 2px solid #9f4a4a;
  border-radius: 0;
  color: #d4a4a4;
  font-family: 'Courier New', monospace;
  font-size: 0.9rem;
  font-weight: bold;
  cursor: pointer;
  text-transform: uppercase;
  letter-spacing: 1px;
  box-shadow: 0 0 8px rgba(159, 74, 74, 0.3);
  transition: all 0.3s ease;

  &:hover {
    background: #702828;
    border-color: #bf6262;
    box-shadow: 0 0 12px rgba(191, 98, 98, 0.5);
    transform: scale(1.05);
  }

  &:active {
    transform: scale(0.95);
  }
}

.retro-main {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: calc(100vh - 100px);
  padding: 2rem;
  position: relative;
  z-index: 1;
}

.retro-content {
  background: #2a2a2a;
  border: 3px solid #4a9f4a;
  padding: 2rem;
  width: 100%;
  max-width: 800px;
  box-shadow:
    0 0 15px rgba(74, 159, 74, 0.3),
    inset 0 0 15px rgba(74, 159, 74, 0.05);
  position: relative;
}


.welcome-section {
  display: grid;
  grid-template-columns: 70% 30%;
  gap: 2rem;
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
  border: 4px solid #a8a832;
  padding: 1rem;
  background: #2d2d2d;
  box-shadow: 0 0 20px rgba(168, 168, 50, 0.4);
  border-radius: 0;
}

.retro-kitty-large {
  width: 300px;
  height: 300px;
  object-fit: cover;
  border: 3px solid #4a9f4a;
  background: #1a1a1a;
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
  padding: 1rem;
}

.retro-greeting {
  font-family: 'Courier New', monospace;
  font-size: 2.5rem;
  color: #a8a832;
  text-shadow: 0 0 12px #a8a832;
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

.corner-decoration {
  position: absolute;
  font-family: 'Courier New', monospace;
  font-size: 1.5rem;
  color: #4a9f4a;
  text-shadow: 0 0 4px #4a9f4a;

  &.top-left {
    top: 10px;
    left: 10px;
  }

  &.top-right {
    top: 10px;
    right: 10px;
  }

  &.bottom-left {
    bottom: 10px;
    left: 10px;
  }

  &.bottom-right {
    bottom: 10px;
    right: 10px;
  }
}

@keyframes flicker {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.98; }
}


@media (max-width: 768px) {
  .retro-main {
    padding: 1rem;
  }

  .retro-content {
    padding: 1.5rem;
  }

  .welcome-section {
    grid-template-columns: 1fr;
    gap: 1.5rem;
  }

  .retro-kitty-large {
    width: 200px;
    height: 200px;
  }

  .retro-greeting {
    font-size: 2rem;
  }

  .header-content {
    flex-direction: column;
    gap: 1rem;
    text-align: center;
  }

  .system-info {
    font-size: 1rem;
  }
}
</style>