<script lang="ts">
    import Expand from "$components/Expand.svelte";

    export let style = "", hoverScale = false, hoverCursor = false, _fade = false;
    export let blur = false, shadow = false, textCenter = false, background = '';
    export let onClick = () => null;
</script>

<div class="box" class:hoverScale {style} on:click={onClick}
     style:box-shadow={shadow ? '0 10px 10px 0 rgba(0, 0, 0, 0.3)' : ''} style:text-align={textCenter ? 'center' : ''}
     style:background={background} style:cursor={hoverCursor ? 'cursor' : ''}>
    {#if blur}
        <div style="position: absolute;left:0;right:0;top:0;bottom: 0;"></div>
    {/if}
    <Expand>
        <slot/>
    </Expand>
</div>

<style lang="scss">
  .box {
    height: fit-content;
    border-radius: 20px;
    transition: 0.2s;

    .hoverScale:hover {
      scale: 1.04;
    }
  }
</style>
