<script setup>
import { ref, computed } from 'vue'

// Reactive variables for user input (used for two-way binding)
const u = ref('')
const v = ref('')
const horizontalFovDeg = ref('69.0')  // Default: 69 degrees
const verticalFovDeg = ref('51.0')    // Default: 51 degrees

// Utility function to convert degrees to radians
function toRadians(degrees) {
  return degrees * Math.PI / 180
}

// Computed property to calculate the required camera distance
const results = computed(() => {
  // Parse input values as floats
  const uVal = parseFloat(u.value)
  const vVal = parseFloat(v.value)
  const hFov = parseFloat(horizontalFovDeg.value)
  const vFov = parseFloat(verticalFovDeg.value)

  // Input validation
  if (
    isNaN(uVal) || isNaN(vVal) || isNaN(hFov) || isNaN(vFov) ||
    uVal <= 0 || vVal <= 0 || hFov <= 0 || vFov <= 0 ||
    hFov >= 180 || vFov >= 180
  ) {
    return null
  }

  // Convert FoV values from degrees to radians
  const horizontalFovRad = toRadians(hFov)
  const verticalFovRad = toRadians(vFov)

  // Calculate the required distance in each direction
  const horizontalDistance = (uVal / 2) / Math.tan(horizontalFovRad / 2)
  const verticalDistance = (vVal / 2) / Math.tan(verticalFovRad / 2)

  // Final required distance is the maximum of both to fully cover the area
  const requiredDistance = Math.max(horizontalDistance, verticalDistance)

  return {
    horizontalDistance,
    verticalDistance,
    requiredDistance
  }
})
</script>

<template>
  <div style="max-width: 650px; margin: auto; padding: 2rem; font-family: sans-serif;">
    <h1>Required Distance Calculator</h1>

    <p>This tool calculates the minimum required distance from a camera based on the Field of View (FoV) and the area to be captured.</p>

    <div style="margin-top: 1rem;">
      <label>
        📐 Horizontal FoV [degrees]:
        <input type="number" v-model="horizontalFovDeg" min="1" max="179" step="0.1" />
      </label>
    </div>

    <div style="margin-top: 1rem;">
      <label>
        📐 Vertical FoV [degrees]:
        <input type="number" v-model="verticalFovDeg" min="1" max="179" step="0.1" />
      </label>
    </div>

    <div style="margin-top: 2rem;">
      <label>
        ↔️ Horizontal range (u) [m]:
        <input type="number" v-model="u" min="0" step="0.01" />
      </label>
    </div>

    <div style="margin-top: 1rem;">
      <label>
        ↕️ Vertical range (v) [m]:
        <input type="number" v-model="v" min="0" step="0.01" />
      </label>
    </div>

    <div v-if="results !== null" style="margin-top: 2rem;">
      <h2>✅ Calculation Results</h2>
      <ul>
        <li>🔹 Required distance (horizontal): <strong>{{ results.horizontalDistance.toFixed(2) }} m</strong></li>
        <li>🔹 Required distance (vertical): <strong>{{ results.verticalDistance.toFixed(2) }} m</strong></li>
        <li>🔸 <strong>Final required distance (to cover both): {{ results.requiredDistance.toFixed(2) }} m</strong></li>
      </ul>
    </div>

    <div v-else style="margin-top: 2rem; color: gray;">
      Please enter valid numerical values for u, v, and both FoV values.<br>
      * Note: FoV must be less than 180 degrees.
    </div>
  </div>
</template>
