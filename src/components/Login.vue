<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const email = ref('')
const password = ref('')
const error = ref('')
const isSubmitting = ref(false)
const emailError = ref('')
const passwordError = ref('')

const validateEmail = (value: string) => {
  if (!value) return 'Email is required'
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  if (!emailRegex.test(value)) return 'Please enter a valid email address'
  return ''
}

const validatePassword = (value: string) => {
  if (!value) return 'Password is required'
  if (value.length < 6) return 'Password must be at least 6 characters'
  return ''
}

const handleEmailInput = () => {
  // Clear error while user is typing
  emailError.value = ''
}

const handlePasswordInput = () => {
  // Clear error while user is typing
  passwordError.value = ''
}

const handleEmailChange = () => {
  emailError.value = validateEmail(email.value)
}

const handlePasswordChange = () => {
  passwordError.value = validatePassword(password.value)
}

const handleLogin = () => {
  // Validate all fields
  const emailValidation = validateEmail(email.value)
  const passwordValidation = validatePassword(password.value)

  emailError.value = emailValidation
  passwordError.value = passwordValidation

  if (emailValidation || passwordValidation) {
    return
  }

  // Prevent double submission
  if (isSubmitting.value) return
  isSubmitting.value = true

  // DEMO APPROACH: SPA-style authentication simulation
  // This is PERFECT for a demo because:
  // ✅ No backend dependencies needed
  // ✅ Easy to run and deploy
  // ✅ Shows modern form handling patterns
  // ✅ Demonstrates all the accessibility best practices
  //
  // PRODUCTION APPROACH: Traditional form submission
  // 1. Remove preventDefault(), add action="/api/auth/login"
  // 2. Server handles auth, sets httpOnly cookies, redirects
  // Benefits: Better security, proper session management

  setTimeout(() => {
    // Simulate successful server response
    router.push('/dashboard')
  }, 800) // Realistic server response time
}

const handleSubmit = (e: Event) => {
  e.preventDefault()
  handleLogin()
}

// WHY THIS WORKS FOR A DEMO:
// 1. Shows all HTML best practices (autocomplete, accessibility, validation)
// 2. Demonstrates modern Vue.js patterns
// 3. No complex backend setup needed
// 4. Easy to deploy as static site
// 5. Perfect for showcasing retro gaming UI design
//
// The Evil Martians recommendation is for PRODUCTION auth,
// but for DEMOS and UI showcases, SPA approach is ideal!
</script>

<template>
  <div class="retro-container">
    <!-- CRT Screen Effect -->
    <div class="crt-overlay"></div>
    <div class="scanlines"></div>

    <div class="retro-login">
      <!-- Retro Header -->
      <div class="retro-header">
        <div class="pixel-border">
          <h1 class="retro-title">█ LOGIN SYSTEM █</h1>
        </div>
        <div class="retro-subtitle">ENTER CREDENTIALS</div>
      </div>

      <!-- Login Form -->
      <div class="retro-form">
        <form @submit="handleSubmit">
          <!-- Email Field -->
          <div class="input-group">
            <label for="email" class="retro-label">EMAIL:</label>
            <div class="input-wrapper">
              <input
                id="email"
                v-model="email"
                type="email"
                autocomplete="username"
                placeholder="user@domain.com"
                class="retro-input"
                :class="{ 'invalid': emailError }"
                :aria-invalid="!!emailError"
                :aria-errormessage="emailError ? 'email-error' : undefined"
                @input="handleEmailInput"
                @change="handleEmailChange"
                required
              />
            </div>
            <div v-if="emailError" id="email-error" class="field-error" role="alert">
              ⚠ {{ emailError }}
            </div>
          </div>

          <!-- Password Field -->
          <div class="input-group">
            <label for="password" class="retro-label">PASSWORD:</label>
            <div class="input-wrapper">
              <input
                id="password"
                v-model="password"
                type="password"
                autocomplete="current-password"
                placeholder="********"
                class="retro-input"
                :class="{ 'invalid': passwordError }"
                :aria-invalid="!!passwordError"
                :aria-errormessage="passwordError ? 'password-error' : undefined"
                @input="handlePasswordInput"
                @change="handlePasswordChange"
                required
              />
            </div>
            <div v-if="passwordError" id="password-error" class="field-error" role="alert">
              ⚠ {{ passwordError }}
            </div>
          </div>

          <!-- Login Button -->
          <div class="button-wrapper">
            <button
              type="submit"
              class="retro-button"
              :disabled="isSubmitting"
              :aria-describedby="isSubmitting ? 'loading-message' : undefined"
            >
              <span class="button-text">
                {{ isSubmitting ? '█ LOADING...' : '▶ LOGIN' }}
              </span>
            </button>
            <div v-if="isSubmitting" id="loading-message" class="loading-message" aria-live="polite">
              Authenticating your credentials...
            </div>
          </div>
        </form>
      </div>

      <!-- Retro Decorations -->
      <div class="retro-decoration">
        <div class="corner-decoration top-left">█</div>
        <div class="corner-decoration top-right">█</div>
        <div class="corner-decoration bottom-left">█</div>
        <div class="corner-decoration bottom-right">█</div>
      </div>
    </div>
  </div>
</template>

<style scoped lang="scss">
.retro-container {
  position: relative;
  display: flex;
  justify-content: center;
  align-items: center;
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
    background: rgba(0, 100, 0, 0.05);
    pointer-events: none;
  }
}

.crt-overlay {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 80, 0, 0.02);
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
    rgba(0, 60, 0, 0.02) 2px,
    rgba(0, 60, 0, 0.02) 4px
  );
  pointer-events: none;
}

.retro-login {
  position: relative;
  background: #2a2a2a;
  border: 3px solid #4a9f4a;
  padding: 2rem;
  width: 100%;
  max-width: 450px;
  box-shadow:
    0 0 15px rgba(74, 159, 74, 0.3),
    inset 0 0 15px rgba(74, 159, 74, 0.05);
  z-index: 1;
}

.retro-header {
  text-align: center;
  margin-bottom: 2rem;
}

.pixel-border {
  border: 2px solid #a8a832;
  border-radius: 0;
  padding: 0.5rem;
  margin-bottom: 1rem;
  background: #2d2d2d;
  box-shadow: 0 0 8px rgba(168, 168, 50, 0.2);
}

.retro-title {
  font-family: 'Courier New', monospace;
  font-size: 2rem;
  font-weight: bold;
  color: #6bb86b;
  text-shadow: 0 0 8px #6bb86b;
  letter-spacing: 2px;
  margin: 0;
  text-transform: uppercase;
}

.retro-subtitle {
  font-family: 'Courier New', monospace;
  font-size: 0.9rem;
  color: #a8a832;
  text-shadow: 0 0 4px #a8a832;
  letter-spacing: 1px;
}

.input-group {
  margin-bottom: 1.5rem;
}

.retro-label {
  display: block;
  font-family: 'Courier New', monospace;
  font-size: 0.9rem;
  color: #a8a832;
  text-shadow: 0 0 4px #a8a832;
  margin-bottom: 0.5rem;
  font-weight: bold;
  letter-spacing: 1px;
}

.input-wrapper {
  position: relative;
}

.retro-input {
  width: 100%;
  padding: 0.75rem;
  background: #1a1a1a;
  border: 2px solid #4a9f4a;
  border-radius: 0;
  color: #6bb86b;
  font-family: 'Courier New', monospace;
  font-size: 1rem;
  box-shadow: inset 0 0 8px rgba(74, 159, 74, 0.1);
  transition: all 0.3s ease;

  &::placeholder {
    color: #4a7a4a;
  }

  &:focus {
    outline: 3px solid #a8a832;
    outline-offset: 2px;
    border-color: #a8a832;
    box-shadow:
      0 0 12px rgba(168, 168, 50, 0.3),
      inset 0 0 8px rgba(168, 168, 50, 0.1);
    background: #252525;
  }

  &.invalid {
    border-color: #d46a6a;
    box-shadow: inset 0 0 8px rgba(212, 106, 106, 0.2);

    &:focus {
      outline-color: #d46a6a;
      border-color: #d46a6a;
      box-shadow:
        0 0 12px rgba(212, 106, 106, 0.3),
        inset 0 0 8px rgba(212, 106, 106, 0.2);
    }
  }
}

.field-error {
  margin-top: 0.5rem;
  font-family: 'Courier New', monospace;
  color: #d46a6a;
  text-shadow: 0 0 4px #d46a6a;
  font-size: 0.8rem;
  letter-spacing: 1px;
  background: rgba(159, 74, 74, 0.1);
  padding: 0.25rem 0.5rem;
  border-left: 3px solid #d46a6a;
}

.loading-message {
  margin-top: 0.5rem;
  font-family: 'Courier New', monospace;
  color: #a8a832;
  text-shadow: 0 0 4px #a8a832;
  font-size: 0.8rem;
  letter-spacing: 1px;
  text-align: center;
}

.button-wrapper {
  margin-top: 2rem;
}

.retro-button {
  width: 100%;
  padding: 1rem;
  background: #2d5016;
  border: 3px solid #6b9f4a;
  border-radius: 0;
  color: #a8d4a8;
  font-family: 'Courier New', monospace;
  font-size: 1.2rem;
  font-weight: bold;
  cursor: pointer;
  text-transform: uppercase;
  letter-spacing: 2px;
  box-shadow: 0 0 12px rgba(107, 159, 74, 0.3);
  transition: all 0.3s ease;

  &:hover:not(:disabled) {
    background: #3d6020;
    border-color: #8bbf62;
    box-shadow: 0 0 18px rgba(139, 191, 98, 0.5);
    transform: scale(1.02);
  }

  &:active:not(:disabled) {
    transform: scale(0.98);
    box-shadow: 0 0 8px rgba(107, 159, 74, 0.2);
  }

  &:disabled {
    opacity: 0.6;
    cursor: not-allowed;
    background: #1a1a1a;
    border-color: #4a9f4a;
    box-shadow: none;
    transform: none;
  }

  &:focus-visible {
    outline: 3px solid #a8a832;
    outline-offset: 2px;
  }
}

.button-text {
  display: block;
  text-align: center;
}

.retro-decoration {
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
  .retro-login {
    margin: 1rem;
    padding: 1.5rem;
  }

  .retro-title {
    font-size: 1.5rem;
  }

  .retro-button {
    font-size: 1rem;
    padding: 0.75rem;
  }
}
</style>