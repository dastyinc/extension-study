<script lang="ts">
    import playSrc from "$static/Play.svg?url";
    import pauseSrc from "$static/pause.svg?url";
    import CircleProgressBar from "./CircleProgressBar.svelte";
    import {getContext} from "svelte";

    export let play = false, status;
    export let clicked = false;
    export let resetTimer = () => {
        
    };
    let hours, minutes, seconds;

    let studyTime = getContext('studyTime');

    $: {
        hours = Math.floor($studyTime / 3600);
        if (hours < 10) {
            hours = "0" + hours;
        }
    }

    $: {
        minutes = Math.floor($studyTime / 60 - hours * 60);
        if (minutes < 10) {
            minutes = "0" + minutes;
        }
    }

    $: {
        seconds = Math.floor($studyTime - hours * 3600 - minutes * 60);
        if (seconds < 10) {
            seconds = "0" + seconds;
        }
    }

</script>

<div>
    <div class="status">{status}</div>
    <div style="display: flex; justify-content: space-between">
        <div class="button">
            <img src={!play ? playSrc : pauseSrc} on:click={() => {play = !play; clicked = true}}/>
        </div>
        <div class="button">
            <CircleProgressBar bind:play ratio={$studyTime / 86400} hours={hours} minutes={minutes} seconds={seconds} resetTimer={resetTimer}/>
        </div>
    </div>
</div>

<style lang="scss">
  .status {
    margin-top: 0.5rem;
    color: #28222d;
    font-size: 1.5rem;
    font-weight: bold;
  }

  .button {
    height: 16rem;
    display: flex;
    align-items: center;
  }

  img {
    margin: auto;
  }

  img:hover {
    cursor: pointer;
  }
</style>