<template>
  <router-view />
</template>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Courier New', monospace;
  background: #1a1a1a;
  color: #6bb86b;
  overflow-x: hidden;
}

/* Global retro styles */
.retro-text {
  font-family: 'Courier New', monospace !important;
  image-rendering: pixelated;
}

/* Ensure all inputs and buttons have pixelated rendering */
input, button, textarea, select {
  font-family: 'Courier New', monospace;
  image-rendering: pixelated;
}

/* Global glow effects for interactive elements */
button:focus, input:focus {
  outline: 2px solid #a8a832;
  outline-offset: 2px;
}

/* Retro cursor */
* {
  cursor: default;
}

button {
  cursor: pointer;
  border: none;
  -webkit-appearance: none;
  appearance: none;
}

input[type="text"], input[type="email"], input[type="password"] {
  cursor: text;
}

/* Disable text selection for a more retro feel */
* {
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

input, textarea {
  -webkit-user-select: text;
  -moz-user-select: text;
  -ms-user-select: text;
  user-select: text;
}
</style>
