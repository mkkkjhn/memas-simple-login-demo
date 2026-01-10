<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const email = ref('')
const password = ref('')
const error = ref('')
const showPassword = ref(false)
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

const togglePasswordVisibility = () => {
  showPassword.value = !showPassword.value
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

  setTimeout(() => {
    // Simulate successful server response
    router.push('/dashboard')
  }, 800)
}

const handleSubmit = (e: Event) => {
  e.preventDefault()
  handleLogin()
}
</script>

<template>
  <div class="retro-container">
    <!-- CRT Screen Effect -->
    <div class="crt-overlay"></div>
    <div class="scanlines"></div>

    <div class="retro-login">

      <div class="retro-header">
        <div class="pixel-border">
          <h1 class="retro-title">█ LOGIN SYSTEM █</h1>
        </div>
        <div class="retro-subtitle">ENTER CREDENTIALS</div>
      </div>

      <div class="retro-form">
        <form @submit="handleSubmit">

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

          <div class="input-group">
            <label for="password" class="retro-label">PASSWORD:</label>
            <div class="input-wrapper">
              <input
                id="password"
                v-model="password"
                :type="showPassword ? 'text' : 'password'"
                autocomplete="current-password"
                placeholder="********"
                class="retro-input"
                :class="{ 'invalid': passwordError }"
                :aria-invalid="!!passwordError"
                :aria-errormessage="passwordError ? 'password-error' : undefined"
                aria-describedby="password-toggle-desc"
                @input="handlePasswordInput"
                @change="handlePasswordChange"
                required
              />
              <button
                type="button"
                class="password-toggle"
                :aria-label="showPassword ? 'Hide password' : 'Show password'"
                :aria-pressed="showPassword"
                aria-describedby="password-toggle-desc"
                @click="togglePasswordVisibility"
              >
                <svg
                  v-if="!showPassword"
                  xmlns="http://www.w3.org/2000/svg"
                  width="20"
                  height="20"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  class="password-icon"
                >
                  <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                  <path d="M10 12a2 2 0 1 0 4 0a2 2 0 0 0 -4 0"/>
                  <path d="M21 12c-2.4 4 -5.4 6 -9 6c-3.6 0 -6.6 -2 -9 -6c2.4 -4 5.4 -6 9 -6c3.6 0 6.6 2 9 6"/>
                </svg>
                <svg
                  v-else
                  xmlns="http://www.w3.org/2000/svg"
                  width="20"
                  height="20"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                  class="password-icon"
                >
                  <path stroke="none" d="M0 0h24v24H0z" fill="none"/>
                  <path d="M10.585 10.587a2 2 0 0 0 2.829 2.828"/>
                  <path d="M16.681 16.673a8.717 8.717 0 0 1 -4.681 1.327c-3.6 0 -6.6 -2 -9 -6c1.272 -2.12 2.712 -3.678 4.32 -4.674m2.86 -1.146a9.055 9.055 0 0 1 1.82 -.18c3.6 0 6.6 2 9 6c-.666 1.11 -1.379 2.067 -2.138 2.87"/>
                  <path d="M3 3l18 18"/>
                </svg>
              </button>
            </div>
            <div v-if="passwordError" id="password-error" class="field-error" role="alert">
              ⚠ {{ passwordError }}
            </div>
            <span id="password-toggle-desc" class="sr-only">
              Toggle to show or hide password text
            </span>
          </div>

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
  display: flex;
  align-items: center;
}

.password-toggle {
  position: absolute;
  right: 10px;
  top: 50%;
  transform: translateY(-50%);
  background: rgba(26, 26, 26, 0.8);
  border: 2px solid #4a9f4a;
  border-radius: 0;
  color: #6bb86b;
  cursor: pointer;
  padding: 0.25rem;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  transition: all 0.3s ease;

  &:hover {
    background: rgba(74, 159, 74, 0.1);
    border-color: #a8a832;
    box-shadow: 0 0 8px rgba(168, 168, 50, 0.3);
  }

  &:focus-visible {
    outline: 3px solid #a8a832;
    outline-offset: 2px;
  }

  &:active {
    transform: translateY(-50%) scale(0.95);
  }
}

.password-icon {
  color: #6bb86b;
  transition: all 0.3s ease;
  filter: drop-shadow(0 0 3px rgba(107, 184, 107, 0.5));
}

.password-toggle:hover .password-icon {
  color: #a8a832;
  filter: drop-shadow(0 0 5px rgba(168, 168, 50, 0.7));
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

.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
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

  &:focus-visible .password-icon {
    color: #a8a832;
    filter: drop-shadow(0 0 6px rgba(168, 168, 50, 0.8));
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