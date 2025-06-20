<template>
  <div class="about">
    <div class="documentation p-4">
  <h2>Official Documentation</h2>
  <p>This tool helps you generate <strong>AAA-compliant color combinations</strong> while preserving your brand's original visual identity as much as possible.</p>

  <h3>1. Overview</h3>
  <p>
    The tool takes a <code>text color</code> and a <code>background color</code> as input, evaluates their contrast using WCAG 2.1 standards,
    and suggests a minimally adjusted alternative color (if needed) to meet accessibility thresholds.
  </p>

  <h3>2. Methodology</h3>
  <ul>
    <li><strong>Color Parsing:</strong> All inputs are parsed using <code>colorjs.io</code> to ensure valid color formats.</li>
    <li><strong>Contrast Evaluation:</strong> Contrast is calculated using the WCAG 2.1 ratio formula:
      <code>(L1 + 0.05) / (L2 + 0.05)</code>.</li>
    <li><strong>Target Thresholds:</strong>
      <ul>
        <li>AAA compliance: ratio ≥ <strong>7.00</strong></li>
        <li>AA fallback: ratio ≥ <strong>4.50</strong></li>
      </ul>
    </li>
    <li><strong>Color Adjustment Strategy:</strong> We only adjust the <code>OKLCH lightness</code> channel to retain the original hue and chroma.
      This ensures minimal perceptual drift while improving contrast.</li>
  </ul>

  <h3>3. Algorithm Steps</h3>
  <ol>
    <li>Receive <code>text</code> and <code>background</code> colors.</li>
    <li>If original contrast ≥ 7.0, no change needed (passes AAA).</li>
    <li>If not, generate a range of candidate text colors by tweaking lightness ±10% in small increments.</li>
    <li>Evaluate contrast for each candidate.</li>
    <li>Select the color with the <em>highest contrast ratio</em>.</li>
    <li>
      Recommendation logic:
      <ul>
        <li>If best contrast ≥ 7.0 → Suggest as AAA alternative.</li>
        <li>If best contrast ≥ 4.5 → Suggest as AA fallback.</li>
        <li>If best contrast &lt; 4.5 → No recommendation possible.</li>
      </ul>
    </li>
  </ol>

  <h3>4. Output</h3>
  <p>
    The tool displays:</p>
    <ul>
      <li>Original color contrast and rating</li>
      <li>Recommended color (if any), shown with preview box</li>
      <li>Hex code of the suggestion</li>
    </ul>
  

  <h3>5. Reference</h3>
  <p>
    Full methodology and justification are detailed in our research paper:<br>
    <a href="https://doi.org/10.48550/arXiv.2506.10324" target="_blank">
      https://doi.org/10.48550/arXiv.2506.10324
    </a>
  </p>

  <h3>6. FAQ</h3>
  <p><strong>Q. Why adjust only the lightness?</strong><br>
  A. Adjusting lightness in OKLCH minimizes visible change while maximizing contrast improvement.</p>

  <p><strong>Q. Why is AAA recommended over AA?</strong><br>
  A. AAA ensures readability across more edge cases (e.g., small fonts, low vision). We default to the stricter standard where feasible.</p>

  <h3>7. Tech Stack</h3>
  <ul>
    <li><strong>Vue 3</strong> for reactivity and interactivity</li>
    <li><strong>Color.js</strong> for color conversions and WCAG contrast calculation</li>
    <li><strong>Bootstrap</strong> for layout and styling consistency</li>
  </ul>

  <hr />
  <p class="text-alert">
    Built for accessible design systems. If you'd like to contribute or extend this, feel free to reach out.
  </p>
</div>

  </div>
</template>

<style>
@media (min-width: 1024px) {
  .about {
    min-height: 100vh;
    display: flex;
    align-items: center;
  }
}
</style>
