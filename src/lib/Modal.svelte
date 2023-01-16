<script lang="ts">
    import {fade} from "svelte/transition";
    import Box from "./Box.svelte";

    export let showModal = false;
</script>

{#if showModal}
    <div class="modal-background" on:click={() => showModal = false}>
        <div transition:fade>
            <Box center background="#ffffff" style="padding: 3.125rem;">
                <slot/>
            </Box>
        </div>
    </div>
{/if}

<style lang="scss">
  .modal-background {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.6);
    display: flex;
    justify-content: center;
    align-items: center;
  }
</style>
