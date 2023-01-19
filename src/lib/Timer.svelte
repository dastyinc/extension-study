<script lang="ts">
    import playSrc from '$static/Play.svg?url';
    import pauseSrc from '$static/pause.svg?url';

    export let StartTimer = () => null;
    export let StopTimer = () => null;
    export let play = false, hours, minutes, seconds;

    $: {
        if(play){
            StartTimer();
        }
        else StopTimer();
    }
</script>

<div>
    <img src={play ? playSrc : pauseSrc} on:click={() => play = !play}/>
    <div class="time">{hours}</div>
    <div class="time">{minutes}</div>
    <div class="time">{seconds}</div>
</div>

<style lang="scss">
    .time{
        color: black;
    }
    
    img:hover{
        cursor: pointer;
    }
</style>