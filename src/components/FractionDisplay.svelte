<script>
  export let numerator;
  export let denominator;
</script>

<div class="fraction">
  <div class="numerator">{numerator}</div>
  <div class="fraction-bar"></div>
  <div class="denominator">{denominator}</div>
</div>

<style>
  .fraction {
    display: flex;
    flex-direction: column;
    align-items: center;
    font-size: 1em;
    min-width: 2em;
  }
  .numerator {
    padding-bottom: 0.1em;
  }
  .fraction-bar {
    border-top: 2px solid currentColor;
    width: 100%;
    margin: 0.05em 0;
  }
  .denominator {
    padding-top: 0.1em;
  }
</style>
