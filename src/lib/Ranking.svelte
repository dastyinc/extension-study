<script lang="ts">
    let testRanking =[
        {user : "six", time : "07:44:02"},
        {user : "three", time : "10:44:02"},
        {user : "one", time : "12:44:02"},
        {user : "two", time : "11:44:02"},
        {user : "four", time : "09:44:02"},
        {user : "five", time : "08:44:02"},
        {user : "eight", time : "05:44:02"},
        {user : "seven", time : "06:44:02"},
    ]

    $:{
        testRanking.sort(function(a,b){
            return b.time.localeCompare(a.time);
        })
    }
</script>

<div class="container">
    {#each testRanking as value, i}
        <div style="display: flex; margin-bottom: 0.625rem; justify-content: space-between">
            <div class="rank">{i + 1}</div>
            <div class="user">{value.user}</div>
            <div class="time">{value.time}</div>
        </div>
    {/each}
</div>

<style lang="scss">
    .container{
        max-height: 14rem;
        overflow-y: scroll;
    }
    .rank{
        border: solid 1px #28222d;
        border-radius: 50%;
        color: #28222d;
        font-family: NotoSansKR;
        font-size: 0.75rem;
    }
    .user{
        color: #28222d;
        font-family: NotoSansKR;
        font-size: 1rem;
    }
    .time{
        color: #28222d;
        font-family: NotoSansKR;
        font-size: 0.563rem;
    }
</style>