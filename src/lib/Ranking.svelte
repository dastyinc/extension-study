<script lang="ts">
    import Poll from "./Poll.svelte";

    let testRanking = [
        {user: "hogun", time: 38642},
        {user: "wagyu", time: 45842},
        {user: "busutsutbu", time: 42242},
        {user: "ray", time: 35042},
        {user: "swishee", time: 31442}
    ]

    $:testRanking.sort(function (a, b) {
        return b.time.toString().localeCompare(a.time.toString());
    })

    String.prototype.toHHMMSS = function () {
        let sec_num = parseInt(this, 10);
        let hours = Math.floor(sec_num / 3600);
        let minutes = Math.floor((sec_num - (hours * 3600)) / 60);
        let seconds = sec_num - (hours * 3600) - (minutes * 60);

        if (hours < 10) {
            hours = "0" + hours;
        }
        if (minutes < 10) {
            minutes = "0" + minutes;
        }
        if (seconds < 10) {
            seconds = "0" + seconds;
        }
        return hours + ':' + minutes + ':' + seconds;
    }
</script>

<div class="container">
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
                <div class="time">{value.time.toString().toHHMMSS()}</div>
            </div>
        </div>
    {/each}
</div>

<style lang="scss">
  .container {
    height: 16rem;
    overflow-y: scroll;
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