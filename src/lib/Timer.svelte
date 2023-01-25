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

  let timeArr = [];
  let timeObj;
  let time_id;
  let intervalId;
  let time = 0;
  let clicked = false;
  const _time = 0;

  $: hours = Math.floor(time / 3600);
  $: minutes = Math.floor(time / 60);
  $: seconds = Math.floor(time - hours * 3600 - minutes * 60);

  onMount(() => {
    getTime();
    initTimeArr();
  })

  async function getTime() {
    timeObj = await api(`/timer/time/${user_id}`);
    timeArr = timeObj.time;
    console.log(timeArr)
  }

  async function initTimeArr() {
    if (timeArr?.length === 0) {
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
    await api(`/timer/time/${time_id}`, "DELETE");
    await api(`/timer/time`, { user_id, user_name, channel_id: channel, time });
  }

  $: {
    if (play && clicked) {
      startTimer();
    } 
    else if(clicked) stopTimer();
  }
</script>

<div>
  <div class="name">수학의 정석 5단원 연습문제 풀기</div>
    <div style="height:7.687rem;"> </div>
  <img src={play ? playSrc : pauseSrc} on:click={() => {play = !play; clicked = true}} />
  <div class="time">{hours}::</div>
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

  .name {
    width: 15.938rem;
  height: 4.375rem;
  margin: 0.5rem 0 4.063rem 0.5rem;
  object-fit: contain;
  font-family: NotoSansKR;
  font-size: 1.5rem;
  font-weight: bold;
  font-stretch: normal;
  font-style: normal;
  line-height: normal;
  letter-spacing: normal;
  text-align: left;
  color: #28222d;
  }
</style>
