<script lang="ts">
  import playSrc from "$static/Play.svg?url";
  import pauseSrc from "$static/pause.svg?url";
  import { getContext, onDestroy, onMount } from "svelte";

  export let play = false, hours, minutes, seconds, status;

  const { api } = getContext("utils");
  const { user_id, user_name } = getContext("account");
  const channel = getContext("channel");
  
  let timeArr = [];
  let timeObj;
  let time_id;
  let intervalId;
  let time = 0;
  let clicked = false;
  const _time = 0;
  
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
  
  onMount(() => {
    getTime();
    initTimeArr();
  })

  onDestroy(() => {
    play = false;
  })

  async function getTime() {
    timeObj = await api(`/timer/time/${user_id}`);
    timeArr = timeObj.time;
    time = timeArr[0]?.time;
    console.log(timeArr)
  }

  async function initTimeArr() {
    await getTime();
    if (timeArr.length === 0) {
      await api(`/timer/time`, {
        user_id,
        user_name,
        channel_id: channel,
        time: _time,
      });
    }
  }

  async function startTimer() {
    await getTime();
    time = timeArr[0].time;
    time_id = timeArr[0].time_id;
    clearInterval(intervalId);
    intervalId = setInterval(() => {
      if (play) time++;
    }, 1000);
  }

  async function stopTimer() {
    await api(`/timer/time/${time_id}`, {}, "DELETE");
    await api(`/timer/time`, { user_id, user_name, channel_id: channel, time });
  }
</script>

<div>
  <div class="status">{status}</div>
  <div style="display: flex; justify-content: space-between">
    <div class="button"><img src={!play ? playSrc : pauseSrc} on:click={() => {play = !play; clicked = true}} /></div>
    <div class="button"><div class="time">{hours}:{minutes}:{seconds}</div></div>
  </div>
</div>

<style lang="scss">
  .time {
    color: #28222d;
    font-size: 1.438rem;
    font-weight: bold;
    font-family: NotoSansKR;
  }

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