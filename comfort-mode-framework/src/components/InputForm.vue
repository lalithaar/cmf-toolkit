<script setup>
import { ref, computed, reactive, watchEffect } from 'vue'
import Color from 'colorjs.io'

const maxPairs = 3
const colorPairs = reactive([
  { text: '#000000', bg: '#ffffff', recommendedText: null, recommendedContrast: null }
])

// Helpers
function safeColor(value) {
  try {
    return new Color(value)
  } catch {
    return null
  }
}

function generateCandidates(baseColor, range = 10, step = 0.01) {
  const candidates = []
  for (let delta = -range; delta <= range; delta++) {
    const clone = baseColor.clone()
    clone.oklch.l = Math.min(1, Math.max(0, clone.oklch.l + delta * step))
    candidates.push(clone)
  }
  return candidates
}

function getBestContrastColor(baseText, background) {
  const candidates = generateCandidates(baseText)
  let best = null
  let bestContrast = 0
  for (const c of candidates) {
    const ratio = background.contrast(c, "WCAG21")
    if (ratio > bestContrast) {
      best = c
      bestContrast = ratio
    }
  }
  return { color: best, ratio: bestContrast }
}

// Watch & update per pair
function updateRecommendation(pair) {
  const t = safeColor(pair.text)
  const b = safeColor(pair.bg)
  if (!t || !b) {
    pair.recommendedText = null
    pair.recommendedContrast = null
    return
  }

  const original = b.contrast(t, "WCAG21")
  const { color, ratio } = getBestContrastColor(t, b)

  if (ratio > original) {
    pair.recommendedText = color
    pair.recommendedContrast = ratio
  } else {
    pair.recommendedText = null
    pair.recommendedContrast = null
  }
}

// Auto-update for each pair
watchEffect(() => {
  colorPairs.forEach(updateRecommendation)
})

function addNewPair() {
  if (colorPairs.length < maxPairs) {
    colorPairs.push({
      text: '#000000',
      bg: '#ffffff',
      recommendedText: null,
      recommendedContrast: null
    })
  }
}
</script>

<template>
  <div class="container my-4">
    <h3 class="mb-3">Accessible Contrast Checker</h3>

    <div
      class="row mb-4"
      v-for="(pair, index) in colorPairs"
      :key="index"
    >
      <div class="col-md-5 mb-3">
        <label class="form-label">Text Color</label>
        <input v-model="pair.text" type="color" class="form-control form-control-color" />
      </div>
      <div class="col-md-5 mb-3">
        <label class="form-label">Background Color</label>
        <input v-model="pair.bg" type="color" class="form-control form-control-color" />
      </div>

      <div class="col-12">
        <p>Contrast Ratio: {{ safeColor(pair.bg)?.contrast(safeColor(pair.text), "WCAG21")?.toFixed(2) ?? '--' }}</p>

        <div v-if="safeColor(pair.bg)?.contrast(safeColor(pair.text), 'WCAG21') >= 7">
          ✅ This color combination meets AAA contrast (7.0+)
        </div>

        <div v-else-if="pair.recommendedText && pair.recommendedContrast >= 7">
          ⚠️ Try this text color instead:  {{ pair.recommendedText.to('srgb').toString({ format: 'hex' }) }}
          <div class="d-flex my-2 gap-3">
            <div class="box" :style="{ color: pair.text, backgroundColor: pair.bg }">Original</div>
            <div class="box" :style="{ color: pair.recommendedText.to('srgb').toString({ format: 'hex' }), backgroundColor: pair.bg }">Modified</div>
          </div>
          <p>New contrast: {{ pair.recommendedContrast.toFixed(2) }} (AAA)</p>
        </div>

        <div v-else-if="pair.recommendedText && pair.recommendedContrast >= 4.5">
          ❌ AAA not reachable, but this text color meets AA:   {{ pair.recommendedText.to('srgb').toString({ format: 'hex' }) }}
          <div class="d-flex my-2 gap-3">
            <div class="box" :style="{ color: pair.text, backgroundColor: pair.bg }">Original</div>
            <div class="box" :style="{ color: pair.recommendedText.to('srgb').toString({ format: 'hex' }), backgroundColor: pair.bg }">Modified</div>
          </div>
          <p>New contrast: {{ pair.recommendedContrast.toFixed(2) }} (AA)</p>
        </div>

        <div v-else>
          ❌ Could not find an accessible color for this background.
        </div>
      </div>
    </div>

    <button
      class="btn btn-outline-primary"
      @click="addNewPair"
      :disabled="colorPairs.length >= maxPairs"
    >
      + Add More Pairs
    </button>
  </div>
</template>

<style scoped>
.box {
  min-width: 100px;
  height: 60px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  border: 1px solid #ddd;
  border-radius: 5px;
}
</style>
