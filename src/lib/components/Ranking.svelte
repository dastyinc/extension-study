<script lang="ts">
    import Poll from "./Poll.svelte";

    export let otherTimes;

    let nowHour,
        nowMinutes,
        nowSeconds;

    $: {
      otherTimes?.sort((a, b) => (b.time-b.startTime) - (a.time-a.startTime));
    }

    setInterval(() => {
      let now = new Date();
      nowHour = now.getHours();
      nowMinutes = now.getMinutes();
      nowSeconds = now.getSeconds();
    }, 1000);

    function calcStudyTime(_time, _startTime) {
      let _studyTime = _time + nowHour * 3600 + nowMinutes * 60 + nowSeconds - _startTime;
      let _hours = Math.floor(_studyTime / 3600);
      if (_hours < 10) {
        _hours = "0" + _hours;
      }
      let _minutes = Math.floor((_studyTime % 3600) / 60);
      if (_minutes < 10) {
        _minutes = "0" + _minutes;
      }
      let _seconds = Math.floor(_studyTime % 60);
      if (_seconds < 10) {
        _seconds = "0" + _seconds;
      }
      return (_studyTime > 0 ? _hours + ":" + _minutes + ":" + _seconds : "00:00:00")
    }
</script>

<div class="container">
  {#if otherTimes !== undefined}
    {#each otherTimes as _, i}
      <div style="display: flex; margin-bottom: 0.625rem; justify-content: space-between; width: 100%;">
        <div style="display: flex;">
          <div class="circle">
            <div class="circle-text">{i + 1}</div>
          </div>
          <div class="user">{otherTimes[i].user_name}</div>
        </div>
        <div style="display: flex;">
          <Poll max={otherTimes[0].time+nowHour*3600+nowMinutes*60+nowSeconds-otherTimes[0].startTime} value={otherTimes[i].time+nowHour*3600+nowMinutes*60+nowSeconds-otherTimes[i].startTime}/>
          {#if otherTimes[i].isPaused}
            <div class="time">{calcStudyTime(otherTimes[i].time, otherTimes[i].startTime)}</div>
          {:else}
            {#key nowSeconds}
              <div class="time">{calcStudyTime(otherTimes[i].time, otherTimes[i].startTime)}</div>
            {/key}
          {/if}
        </div>
      </div>
    {/each}
  {/if}
</div>

<style lang="scss">
  .container {
    margin-top: 0.938rem;
    max-height: 13.4rem;
    overflow-x: hidden;
    overflow-y: scroll;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .circle {
    width: 1.438rem;
    height: 1.438rem;
    border: solid 1px #28222d;
    border-radius: 50%;
  }

  .circle-text {
    color: #28222d;
    font-size: 0.75rem;
    width: fit-content;
    position: relative;
    transform: translate(-50%, -50%);
    top: 0.719rem;
    left: 0.719rem;
  }

  .user {
    color: #28222d;
    font-size: 1rem;
    line-height: 1.6rem;
    margin-left: 0.438rem;
  }

  .time {
    color: #28222d;
    font-size: 0.563rem;
    margin-left: 0.474rem;
    line-height: 1.6rem;
  }
</style>