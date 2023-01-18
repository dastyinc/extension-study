<script lang="ts">

    let play = false;
</script>

<div>
    <button on:click={() => play = !play}>{play ? "멈춤" : "시작"}</button>
    
</div>