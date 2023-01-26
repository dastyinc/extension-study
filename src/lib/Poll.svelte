<script lang="ts">
    export let max, value;

</script>

<div class="bar">
    <div class="percentage" style="width: {value / max * 100}%;"></div>
</div>

<style lang="scss">
  .bar {
    width: 6.4rem;
    height: 0.188rem;
    background: #bababa;
    position: relative;
    transform: translate(0, -50%);
    top: 50%;
  }

  .percentage {
    position: absolute;
    left: 0;
    background: #28222d;
    height: 100%;
  }
</style>