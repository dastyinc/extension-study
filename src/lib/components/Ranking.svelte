<script lang="ts">
    import Poll from "./Poll.svelte";

    export let otherTimes;

    let nowHour,
        nowMinutes,
        nowSeconds;

    $: otherTimes.sort((a, b) => {
        return b.time.toString().localeCompare(a.time.toString());
    })

    setInterval(() => {
      let now = new Date();
      nowHour = now.getHours();
      nowMinutes = now.getMinutes();
      nowSeconds = now.getSeconds();
    }, 1000);

    function toHHMMSS(sec_num) {
        let hours = Math.floor(sec_num / 3600).toString().padStart(2, '0');
        let minutes = Math.floor((sec_num - (hours * 3600)) / 60).toString().padStart(2, '0');
        let seconds = (sec_num - (hours * 3600) - (minutes * 60)).toString().padStart(2, '0');

        return `${hours}:${minutes}:${seconds}`;
    }
</script>

<div class="container --scroll">
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
                <div class="time">{toHHMMSS(otherTimes[i].time.toString())}</div>
            </div>
        </div>
    {/each}
</div>

<style lang="scss">
  .container {
    max-height: 15rem;
    overflow-y: auto;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
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