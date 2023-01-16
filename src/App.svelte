<script lang="ts">
    import Box from "$lib/Box.svelte";
    import {slide} from "svelte/transition";
    import moreSrc from "$static/more.svg?url";
    import checkboxSrc from "$static/checkbox.svg?url";
    import checkedSrc from "$static/checked.svg?url";
    import uncheckedSrc from "$static/unchecked.svg?url";
    import closeSrc from "$static/close.svg?url";
    import plusSrc from "$static/plus.svg?url";

    let showTodo = false;
    let testArr = [
        {
            checked: false,
            goal: "수학의 정석 1 끝내기수학의 정석 1 끝내기"
        },
        {
            checked: false,
            goal: "goal 2"
        },
        {
            checked: false,
            goal: "goal 3"
        },
        {
            checked: false,
            goal: "goal 1"
        },
        {
            checked: false,
            goal: "goal 2"
        },
        {
            checked: false,
            goal: "goal 3"
        },
        {
            checked: false,
            goal: "goal 1"
        },
        {
            checked: false,
            goal: "goal 2"
        },
        {
            checked: false,
            goal: "goal 3"
        },
    ];
</script>

<div style="display: flex; border-bottom: 1px solid rgba(255, 255, 255, 0.5);">
    <div class="title">Study</div>
</div>

<div style="float: right; margin-top: -2.4rem; margin-right: 0.444rem"><img src={moreSrc}/></div>

<Box hoverCursor onClick={() => showTodo = !showTodo}
     style="background-color: white; margin-top: 1.219rem; box-shadow: 0 10px 10px 0 rgba(0, 0, 0, 0.3); padding: 0.938rem 1.25rem 0.812rem 1.25rem">
    <div style="display: flex; justify-content: space-between;">
        <div>
            <div class="name" style="color: #ffffff;">Study</div>
            <div class="status" style="color: black; margin-top: 0.625rem">TOEFL 공부 중</div>
            <div style="display: flex; margin: 0.625rem 0 0.875rem 0">
                <img src={checkboxSrc}/>
                <div style="color: black; line-height: 1.5rem; margin-left: 0.312rem">체크 리스트</div>
            </div>
        </div>
        <div class="circle">
            <div class="circle-text">test</div>
        </div>
    </div>

    {#if showTodo}
        <div transition:slide>
            <Box style="background-color: #28222d; box-shadow: 0 10px 10px 0 rgba(0, 0, 0, 0.3); padding: 0.625rem; margin: 20px 0 0.438rem 0">
                <div class="name" style="background-color: white; color: #28222d">Todo-List</div>
                {#each testArr as goal}
                    <div class="goal">
                        <img src={goal.checked ? checkedSrc : uncheckedSrc}/>
                        <div style="margin-left: 0.625rem; width: 75%; font-size: 1rem; font-family: NotoSansKR">{goal.goal}</div>
                        <img class="close" src={closeSrc}/>
                    </div>
                {/each}
                <div style="display: flex; margin-top: 0.625rem">
                    <img src={plusSrc}/>
                    <input on:click|stopPropagation={()=>{}} style="margin-left: 0.625rem;"
                           placeholder="새로운 목표를 세워보세요!"/>
                </div>
            </Box>
        </div>
    {/if}
</Box>

<style lang="scss">
  .title {
    margin: 0 16.813rem 0.594rem 0;
    font-family: NotoSansKR;
    font-size: 1.5rem;
    font-weight: 500;
    color: #dce6f2;
  }

  .goal {
    display: flex;
    margin-top: 0.625rem;
    justify-content: space-between;
    align-items: flex-start;
  }

  .goal > .close {
    opacity: 0;
  }

  .goal:hover > .close {
    opacity: 1;
  }

  .name {
    display: inline-block;
    border-radius: 10px;
    box-shadow: 0 3px 6px 0 rgba(0, 0, 0, 0.16);
    background-color: #28222d;
    font-size: 0.875rem;
    width: fit-content;
    padding: 0.1875rem 0.625rem 0.1875rem 0.625rem;
    line-height: 1.4rem;
  }

  .status {
    font-size: 1.25rem;
    font-weight: bold;
    color: #28222d;
  }

  .circle {
    width: 6.25rem;
    height: 6.25rem;
    border-radius: 50%;
    border: 6px solid #28222d;
  }

  .circle-text {
    margin: 3.125rem 0 0 3.125rem;
    transform: translate(-50%, -50%);
    color: #28222d;
    text-align: center;
    font-size: 1.5rem;
    font-weight: bold;
  }

  span {
    -webkit-user-select: none;
    -moz-user-select: -moz-none;
  }

  input {
    background-color: transparent;
    border: none;
    color: white;
  }

  input:focus {
    outline: none;
  }

  input::placeholder {
    color: white;
    opacity: 0.5;
  }
</style>
