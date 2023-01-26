<script lang="ts">
  import playSrc from "$static/Play.svg?url";
  import pauseSrc from "$static/pause.svg?url";
  import CircleProgressBar from "./CircleProgressBar.svelte";

  export let play = false, hours, minutes, seconds, status;
  export let startTimer = () => {};
  export let getTime = () => {};
  export let stopTimer = () => {};
  export let time_id;
  export let time = 0;
  let clicked = false;
  
  $: {
    hours = Math.floor(time / 3600);
    if(hours < 10){
      hours = "0" + hours;
    }
  }
  $: {
    minutes = Math.floor(time / 60);
    if(minutes < 10){
      minutes = "0" + minutes;
    }
  }
  $: {
    seconds = Math.floor(time - hours * 3600 - minutes * 60);
    if(seconds < 10){
      seconds = "0" + seconds;
    }
  }
  $: {
    if (play && clicked) {
      startTimer();
    } 
    else if(clicked) stopTimer();
  }
</script>

<div>
  <div class="status">{status}</div>
  <div style="display: flex; justify-content: space-between">
    <div class="button"><img src={!play ? playSrc : pauseSrc} on:click={() => {play = !play; clicked = true}} /></div>
    <div class="button"><CircleProgressBar ratio={time / 86400} hours={hours} minutes={minutes} seconds={seconds}/></div>
  </div>
</div>

<style lang="scss">
  .status{
    margin-top: 0.5rem;
    color: #28222d;
    font-family: NotoSansKR;
    font-size: 1.5rem;
    font-weight: bold;
  }

  .button{
    height: 16rem;
    display: flex;
    align-items: center;
  }

  img{
    margin: auto;
  }

  img:hover {
    cursor: pointer;
  }
</style>