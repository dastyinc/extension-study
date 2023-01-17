<script>
    import { fade } from "svelte/transition";
    import {Box} from '@dastyinc/kit-panel';
    export let showModal = false;
</script>
    
    {#if showModal}
        <div class="modal-background" on:click={() => showModal = false} transition:fade></div>
        <Box bindWidth background="#ffffff" 
        style="padding: 3.125rem; z-index: 100; width: 57.75rem; height: 55.875rem; position: absolute; transform:translate(-50%, -50%), top: 50%; left: 50%">
            <slot/>
        </Box>
    {/if}
    
<style>
    .modal-background{
        position: fixed;
        top: 0;
        left: 0;
        background: rgba(0, 0, 0, 0.6);
        z-index: 90;
    }
</style>
    