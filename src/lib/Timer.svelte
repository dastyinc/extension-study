<script lang="ts">
  import playSrc from "$static/Play.svg?url";
  import pauseSrc from "$static/pause.svg?url";
  import { getContext, onMount } from "svelte";

  export let play = false,
    hours,
    minutes,
    seconds;

  const { api } = getContext("utils");
  const { user_id, user_name } = getContext("account");
  const channel = getContext("channel");

  let timeArr;
  let timeObj;
  let time_id;
  let intervalId;
  let time;
  const _time = 0;

  $: hours = Math.floor(time / 3600);
  $: minutes = Math.floor(time / 60);
  $: seconds = Math.floor(time - hours * 3600 - minutes * 60);

  async function getTime() {
    timeObj = await api(`/timer/time/${user_id}`);
    timeArr = timeObj.time;
    console.log(timeArr);
  }

  async function initTimeArr() {
    if (timeArr?.length === 0) {
      console.log("put");
      await api(`/timer/time`, {
        user_id,
        user_name,
        channel_id: channel,
        time: _time,
      });
    }
  }

  function startTimer() {
    getTime();
    time = timeArr[0].time;
    time_id = timeArr[0].time_id;
    clearInterval(intervalId);
    intervalId = setInterval(() => {
      if (play) time++;
    }, 1000);
  }

  async function stopTimer() {
    await api(`/timer/time/${time_id}`, "DELETE");
    await api(`/timer/time`, { user_id, user_name, channel_id: channel, time });
  }

  $: {
    if (play) {
      startTimer();
    } else stopTimer();
  }
</script>

<div>
  <img src={play ? playSrc : pauseSrc} on:click={() => (play = !play)} />
  <div class="time">{hours}</div>
  <div class="time">{minutes}</div>
  <div class="time">{seconds}</div>
</div>

<style lang="scss">
  .time {
    color: black;
  }

  img:hover {
    cursor: pointer;
  }
</style>
