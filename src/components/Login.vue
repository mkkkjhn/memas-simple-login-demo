<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const email = ref('')
const password = ref('')
const error = ref('')
const showPassword = ref(false)
const rememberMe = ref(false)

// Validation states following "reward early, punish late" principle
const emailState = ref<'untouched' | 'valid' | 'invalid'>('untouched')
const passwordState = ref<'untouched' | 'valid' | 'invalid'>('untouched')
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

// Clear errors immediately while typing
const handleEmailInput = () => {
  if (emailState.value === 'invalid') {
    emailState.value = 'untouched'
  }
  // Show success immediately if valid
  if (email.value && validateEmail(email.value) === '') {
    emailState.value = 'valid'
  }
}

const handlePasswordInput = () => {
  if (passwordState.value === 'invalid') {
    passwordState.value = 'untouched'
  }
  // Show success immediately if valid
  if (password.value && validatePassword(password.value) === '') {
    passwordState.value = 'valid'
  }
}

// Late validation
const handleEmailBlur = () => {
  if (email.value) {
    const validation = validateEmail(email.value)
    emailState.value = validation ? 'invalid' : 'valid'
  } else {
    emailState.value = 'untouched'
  }
}

const handlePasswordBlur = () => {
  if (password.value) {
    const validation = validatePassword(password.value)
    passwordState.value = validation ? 'invalid' : 'valid'
  } else {
    passwordState.value = 'untouched'
  }
}

const togglePasswordVisibility = () => {
  showPassword.value = !showPassword.value
}

// Mobile keyboard handling
let resizeTimeout: number
const handleViewportChange = () => {
  // Debounce resize events
  clearTimeout(resizeTimeout)
  resizeTimeout = setTimeout(() => {
    const viewportHeight = window.visualViewport?.height || window.innerHeight
    const windowHeight = window.innerHeight

    // Check if keyboard is likely visible (viewport smaller than window)
    const keyboardVisible = viewportHeight < windowHeight * 0.8

    document.documentElement.style.setProperty('--keyboard-visible', keyboardVisible ? '1' : '0')

    // Scroll focused element into view if needed
    const focusedElement = document.activeElement as HTMLElement
    if (focusedElement && keyboardVisible) {
      setTimeout(() => {
        focusedElement.scrollIntoView({
          behavior: 'smooth',
          block: 'center'
        })
      }, 300)
    }
  }, 100)
}

// Check for existing authentication on mount
const checkExistingAuth = () => {
  const localToken = localStorage.getItem('authToken')
  const sessionToken = sessionStorage.getItem('authToken')

  if (localToken || sessionToken) {
    // User is already authenticated, redirect to dashboard
    router.push('/dashboard')
    return true
  }

  // Restore "Remember Me" preference if it exists
  const rememberPreference = localStorage.getItem('rememberMe')
  if (rememberPreference === 'true') {
    rememberMe.value = true
  }

  return false
}

// Listen for viewport changes (keyboard show/hide)
onMounted(() => {
  if (window.visualViewport) {
    window.visualViewport.addEventListener('resize', handleViewportChange)
  }
  window.addEventListener('resize', handleViewportChange)

  // Check if user is already authenticated
  checkExistingAuth()
})

onUnmounted(() => {
  if (window.visualViewport) {
    window.visualViewport.removeEventListener('resize', handleViewportChange)
  }
  window.removeEventListener('resize', handleViewportChange)
})

const emailErrorMessage = computed(() => {
  return emailState.value === 'invalid' ? validateEmail(email.value) : ''
})

const passwordErrorMessage = computed(() => {
  return passwordState.value === 'invalid' ? validatePassword(password.value) : ''
})

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
    // Simulate successful authentication
    const authToken = 'demo-auth-token-' + Date.now()

    // Store authentication based on "Remember Me" preference
    if (rememberMe.value) {
      // Remember for 30 days
      localStorage.setItem('authToken', authToken)
      localStorage.setItem('userEmail', email.value)
      localStorage.setItem('rememberMe', 'true')
    } else {
      // Session only (cleared when browser closes)
      sessionStorage.setItem('authToken', authToken)
      sessionStorage.setItem('userEmail', email.value)
      // Clear any previous localStorage data
      localStorage.removeItem('authToken')
      localStorage.removeItem('userEmail')
      localStorage.removeItem('rememberMe')
    }

    router.push('/dashboard')
  }, 800)
}

const handleSubmit = (e: Event) => {
  e.preventDefault()
  handleLogin()
}
</script>

<template>
  <div class="retro-container crt-screen">
    <!-- CRT Screen Effect -->
    <div class="crt-overlay"></div>
    <div class="scanlines"></div>

    <div class="retro-box retro-login">

      <div class="retro-header">
        <div class="pixel-border">
          <h1 class="retro-title">█ LOGIN SYSTEM █</h1>
        </div>
        <div class="retro-subtitle">ENTER CREDENTIALS</div>
      </div>

      <div class="retro-form">
        <form @submit="handleSubmit">

          <div class="input-group">
            <label for="email" class="retro-label">
              EMAIL:
              <span v-if="emailState === 'valid'" class="validation-indicator success">✓</span>
            </label>
            <div class="input-wrapper">
              <input
                id="email"
                v-model="email"
                type="email"
                autocomplete="username"
                placeholder="user@domain.com"
                class="retro-input"
                :class="{
                  'input-valid': emailState === 'valid',
                  'input-invalid': emailState === 'invalid'
                }"
                :aria-invalid="emailState === 'invalid'"
                @input="handleEmailInput"
                @blur="handleEmailBlur"
                required
              />
            </div>
            <div v-if="emailErrorMessage" id="email-error" class="field-error" role="alert">
              ⚠ {{ emailErrorMessage }}
            </div>
          </div>

          <div class="input-group">
            <label for="password" class="retro-label">
              PASSWORD:
              <span v-if="passwordState === 'valid'" class="validation-indicator success">✓</span>
            </label>
            <div class="input-wrapper password-input-wrapper">
              <input
                id="password"
                v-model="password"
                :type="showPassword ? 'text' : 'password'"
                autocomplete="current-password"
                placeholder="******"
                class="retro-input"
                :class="{
                  'input-valid': passwordState === 'valid',
                  'input-invalid': passwordState === 'invalid'
                }"
                :aria-invalid="passwordState === 'invalid'"
                aria-describedby="password-toggle-desc"
                @input="handlePasswordInput"
                @blur="handlePasswordBlur"
                required
              />
              <button
                type="button"
                class="password-toggle"
                v-show="password.length > 0"
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
            <div v-if="passwordErrorMessage" id="password-error" class="field-error" role="alert">
              ⚠ {{ passwordErrorMessage }}
            </div>
            <span id="password-toggle-desc" class="sr-only">
              Toggle to show or hide password text
            </span>
          </div>

          <!-- Remember Me Option -->
          <div class="form-options">
            <label class="remember-label">
              <input
                type="checkbox"
                v-model="rememberMe"
                name="remember"
                class="retro-checkbox"
                autocomplete="on"
              />
              <span class="checkbox-custom"></span>
              Remember me for 30 days
            </label>
          </div>

          <div class="form-actions">
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
@use '../assets/variables' as *;

.retro-container {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

.retro-login {
  width: 100%;
  max-width: 450px;
  position: relative;
}

.retro-header {
  text-align: center;
  margin-bottom: $retro-spacing-xl;
}

.input-group {
  margin-bottom: $retro-spacing-lg;
}

.input-wrapper {
  position: relative;
  display: flex;
  align-items: center;
}

.password-input-wrapper .retro-input {
  padding-right: 3rem;
}

.password-toggle {
  position: absolute;
  right: 8px;
  top: 50%;
  transform: translateY(-50%);
  background: transparent;
  border: none !important;
  outline: none;
  box-shadow: none !important;
  -webkit-appearance: none;
  appearance: none;
  color: $retro-text-primary;
  cursor: pointer;
  padding: $retro-spacing-xs;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 32px;
  height: 32px;
  transition: all $retro-transition-base, opacity $retro-transition-base, visibility $retro-transition-base;

  &:hover {
    background: rgba(74, 159, 74, 0.3);
    border: none;
    outline: none;
    box-shadow: none;
  }

  &:focus:not(:focus-visible) {
    outline: none;
    border: none;
  }

  &:focus-visible {
    outline: 3px solid $retro-yellow;
    outline-offset: 2px;
    border: none;
    box-shadow: none;
  }

  &:active {
    transform: translateY(-50%) scale(0.95);
    border: none;
    outline: none;
  }
}

.password-icon {
  color: $retro-text-primary;
  transition: all $retro-transition-base;
  filter: drop-shadow(0 0 3px $retro-glow-green);
}

.password-toggle:hover .password-icon {
  color: $retro-yellow;
  filter: drop-shadow(0 0 8px rgba(168, 168, 50, 0.9));
}

.password-toggle:focus-visible .password-icon {
  color: $retro-yellow;
  filter: drop-shadow(0 0 10px rgba(168, 168, 50, 1));
}

.form-options {
  margin: $retro-spacing-lg 0 $retro-spacing-md 0;
}

.remember-label {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  cursor: pointer;
  font-family: $retro-font-family;
  color: $retro-text-primary;
  font-size: $retro-font-size-md;
  user-select: none;
}

.retro-checkbox {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  width: 0;
  height: 0;
}

.checkbox-custom {
  width: 16px;
  height: 16px;
  border: $retro-border-thin solid $retro-border-primary;
  background: $retro-bg-input;
  position: relative;
  transition: all $retro-transition-base;
  flex-shrink: 0;
}

.retro-checkbox:checked + .checkbox-custom::after {
  content: '✓';
  position: absolute;
  top: -2px;
  left: 1px;
  color: $retro-yellow;
  font-size: 12px;
  font-weight: bold;
}

.remember-label:hover .checkbox-custom {
  border-color: $retro-yellow;
  box-shadow: 0 0 6px $retro-glow-yellow;
}

.retro-checkbox:focus + .checkbox-custom {
  outline: 3px solid $retro-yellow;
  outline-offset: 2px;
}

.loading-message {
  margin-top: $retro-spacing-sm;
  font-family: $retro-font-family;
  color: $retro-yellow;
  text-shadow: 0 0 4px $retro-yellow;
  font-size: $retro-font-size-sm;
  letter-spacing: 1px;
  text-align: center;
}

.form-actions {
  margin-top: $retro-spacing-xl;
}

.retro-button {
  width: 100%;
  padding: $retro-spacing-md;
  background: $retro-green-darker;
  border: $retro-border-medium solid $retro-green-dark;
  border-radius: 0;
  color: $retro-yellow-light;
  font-family: $retro-font-family;
  font-size: $retro-font-size-lg;
  font-weight: bold;
  cursor: pointer;
  text-transform: uppercase;
  letter-spacing: 2px;
  box-shadow: $retro-shadow-sm $retro-glow-green;
  transition: all $retro-transition-base;

  &:hover:not(:disabled) {
    background: #3d6020;
    border-color: $retro-green-light;
    box-shadow: 0 0 18px rgba(139, 191, 98, 0.5);
    transform: scale(1.02);
  }

  &:active:not(:disabled) {
    transform: scale(0.98);
    box-shadow: 0 0 8px rgba(107, 184, 107, 0.2);
  }

  &:disabled {
    opacity: 0.6;
    cursor: not-allowed;
    background: $retro-bg-primary;
    border-color: $retro-border-primary;
    box-shadow: none;
    transform: none;
  }

  &:focus-visible {
    outline: 3px solid $retro-yellow;
    outline-offset: 2px;
  }

  &:focus-visible .password-icon {
    color: $retro-yellow;
    filter: drop-shadow(0 0 10px rgba(168, 168, 50, 1));
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

/* Mobile optimizations for better keyboard handling */
@media (max-width: 768px) {
  .retro-login {
    margin: $retro-spacing-md;
    padding: $retro-spacing-lg;
  }

  .retro-title {
    font-size: $retro-font-size-xl;
  }

  .retro-button {
    font-size: $retro-font-size-base;
    padding: 0.75rem;
    min-height: 48px;
  }

  .password-toggle {
    min-width: 48px;
    min-height: 48px;
  }

  input, button {
    font-size: 16px;
  }

  input:focus, button:focus {
    outline: 3px solid $retro-yellow;
    outline-offset: 2px;
  }

  .password-toggle:focus {
    outline: 3px solid $retro-yellow;
    outline-offset: 1px;
    border: none;
    box-shadow: none;
  }
}

/* Handle landscape orientation and keyboard visibility */
@media (max-height: 600px) and (orientation: landscape) {
  .retro-container {
    padding-top: $retro-spacing-md;
    padding-bottom: $retro-spacing-md;
  }

  .retro-login {
    max-height: calc(100vh - #{$retro-spacing-xl});
    overflow-y: auto;
  }

  .retro-header {
    margin-bottom: $retro-spacing-md;
  }

  .retro-form {
    padding: $retro-spacing-md 0;
  }

  .form-actions {
    margin-top: $retro-spacing-md;
  }
}

/* Specific handling for very small screens */
@media (max-height: 500px) {
  .retro-container {
    padding-top: $retro-spacing-sm;
    padding-bottom: $retro-spacing-sm;
  }

  .retro-login {
    padding: $retro-spacing-md;
  }

  .pixel-border {
    padding: $retro-spacing-xs;
  }

  .retro-title {
    font-size: $retro-font-size-lg;
  }

  .input-group {
    margin-bottom: $retro-spacing-md;
  }

  .form-actions {
    margin-top: $retro-spacing-md;
  }
}

/* Ensure form stays accessible when virtual keyboard appears */
@media (max-width: 768px) and (max-height: 500px) {
  .retro-form {
    display: flex;
    flex-direction: column;
    justify-content: center;
    min-height: calc(100vh - 200px);
  }

  .form-actions {
    margin-top: auto;
    padding-top: $retro-spacing-md;
  }
}

/* Improve touch interaction feedback on mobile */
@media (hover: none) and (pointer: coarse) {
  .retro-button:active {
    transform: scale(0.98);
    transition: transform $retro-transition-fast;
  }

  .password-toggle:active {
    transform: translateY(-50%) scale(0.95);
  }

  // Ensure sufficient spacing between interactive elements
  .input-wrapper {
    margin-bottom: $retro-spacing-sm;
  }

  .field-error {
    margin-top: 0.75rem;
  }
}

/* Dynamic keyboard handling */
.retro-container:has(.retro-login) {
  transition: padding $retro-transition-base;
}

.retro-container:has(.retro-login)[style*="--keyboard-visible: 1"] {
  padding-top: $retro-spacing-md !important;
  padding-bottom: $retro-spacing-md !important;
}

.retro-container:has(.retro-login)[style*="--keyboard-visible: 1"] .retro-login {
  max-height: calc(100vh - #{$retro-spacing-xl});
  transition: max-height $retro-transition-base;
}

/* Better tap targets for mobile */
@media (max-width: 480px) {
  .retro-input {
    padding: $retro-spacing-md 0.75rem;
    min-height: 48px;
  }

  .retro-button {
    padding: $retro-spacing-md #{$retro-spacing-xl};
    min-height: 52px;
  }

  .remember-label {
    font-size: 0.85rem;
    gap: $retro-spacing-sm;
  }

  .checkbox-custom {
    width: 18px;
    height: 18px;
  }

  .form-options {
    margin: $retro-spacing-md 0;
  }

  // Ensure labels are easily tappable
  .retro-label {
    padding: $retro-spacing-xs 0;
    margin-bottom: 0.75rem;
  }
}
</style>