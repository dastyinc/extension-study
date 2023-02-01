<script lang="ts">
    import Poll from "./Poll.svelte";

    let testRanking = [
        {user: "hogun", time: 38642},
        {user: "wagyu", time: 45842},
        {user: "busutsutbu", time: 42242},
        {user: "ray", time: 35042},
        {user: "swishee", time: 31442}
    ]

    $: testRanking.sort((a, b) => {
        return b.time.toString().localeCompare(a.time.toString());
    })

    function toHHMMSS(sec_num) {
        let hours = Math.floor(sec_num / 3600).toString().padStart(2, '0');
        let minutes = Math.floor((sec_num - (hours * 3600)) / 60).toString().padStart(2, '0');
        let seconds = (sec_num - (hours * 3600) - (minutes * 60)).toString().padStart(2, '0');

        return `${hours}:${minutes}:${seconds}`;
    }
</script>

<div class="container --scroll">
    {#each testRanking as value, i}
        <div style="display: flex; margin-bottom: 0.625rem; justify-content: space-between; width: 100%;">
            <div style="display: flex;">
                <div class="circle">
                    <div class="circle-text">{i + 1}</div>
                </div>
                <div class="user">{value.user}</div>
            </div>
            <div style="display: flex;">
                <Poll max={testRanking[0].time} value={testRanking[i].time}/>
                <div class="time">{toHHMMSS(value.time.toString())}</div>
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