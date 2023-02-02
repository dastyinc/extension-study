<script lang="ts">
  export let timer;
  let userTime = 0;

  $: if (timer) console.log(timer);
  $: console.log(timer.isPaused);
  $: if (timer.isPaused == false) {
    // timer.time = timer.time++;
    // userTime = timer.time;
    userTime = timer.time;
    console.log("increase time");
  } else {
    // setInterval(() => {
    //   userTime = timer.time + 1;
    // }, 1000);
    userTime = timer.time;
    console.log(userTime);
  }
  let num = 0;
</script>

<div>
  user id : {timer.user_id} and timer : {userTime}, test : {num}
</div>

<style lang="scss">
</style>
