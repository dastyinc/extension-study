<script lang="ts">
    import {getContext} from "svelte";
    import modalSrc from "$static/modal.png?url";
    import checkboxSrc from "$static/checkbox.svg?url";
    import checkedSrc from "$static/checked.svg?url";
    import uncheckedSrc from "$static/unchecked.svg?url";
    import closeSrc from "$static/close.svg?url";
    import plusSrc from "$static/plus.svg?url";
    import {Box} from "@dastyinc/kit-panel";
    import {slide} from "svelte/transition";

    export let showStudyModal, showTodo, status, _completed, todoList, goal;
    export let alterChecked = () => {
    };
    export let _alterChecked = () => {
    };
    export let deleteTodo = () => {
    };
    export let onKeyPress = () => {
    };
    
    let studyTime = getContext('studyTime');

    let hours, minutes;

    $: {
        hours = Math.floor($studyTime / 3600);
        if (hours < 10) {
            hours = "0" + hours;
        }
    }

    $: {
        minutes = Math.floor($studyTime / 60 - hours * 60);
        if (minutes < 10) {
            minutes = "0" + minutes;
        }
    }
</script>

<div style="display: flex; border-bottom: 1px solid rgba(255, 255, 255, 0.5);">
    <div class="title">Study</div>
</div>

<div style="float: right; margin-top: -2.4rem; margin-right: 0.444rem">
    <img src={modalSrc} class="expand" on:click={() => (showStudyModal = !showStudyModal)}/>
</div>

<Box background="#ffffff" style="margin-top: 1.219rem; padding: 0.938rem 1.25rem 0.812rem 1.25rem;">
    <div style="display: flex; justify-content: space-between;">
        <div class="top-div" on:click={() => (showTodo = !showTodo)}>
            <div class="name" style="color: #ffffff;">Study</div>
            <div class="status" style="color: black; margin-top: 0.625rem">{status}</div>
            <div style="display: flex; margin: 0.625rem 0 0.875rem 0">
                <img src={checkboxSrc}/>
                <div style="color: black; line-height: 1.5rem; margin-left: 0.312rem">{_completed}
                    / {todoList.length}</div>
            </div>
        </div>
        <div class="circle">
            <div class="circle-text">{hours}:{minutes}</div>
        </div>
    </div>

    {#if showTodo}
        <div transition:slide>
            <Box background="#28222d" shadow style="padding: 0.625rem; margin: 20px 0 0.438rem 0;">
                <div class="name" style="background-color: white; color: #28222d">Todo-List</div>
                {#each todoList as goal}
                    {#if !goal.completed}
                        <div class="goal">
                            <img class="img" src={uncheckedSrc} on:click={alterChecked(goal)}/>
                            <div on:click={() => status = goal.goal} class="text"
                                 style="margin-left: 0.625rem; width: 75%; font-size: 1rem;">
                                {goal.goal}
                            </div>
                            <img class="close img" src={closeSrc} on:click|stopPropagation={deleteTodo(goal.todo_id)}/>
                        </div>
                    {/if}
                {/each}

                {#each todoList as goal}
                    {#if goal.completed}
                        <div class="goal">
                            <img class="img" src={checkedSrc} on:click={_alterChecked(goal)}/>
                            <div style="margin-left: 0.625rem; width: 75%; font-size: 1rem;">{goal.goal}</div>
                            <img class="close img" src={closeSrc} on:click|stopPropagation={deleteTodo(goal.todo_id)}/>
                        </div>
                    {/if}
                {/each}
                <div style="display: flex; margin-top: 0.625rem">
                    <img src={plusSrc}/>
                    <input bind:value={goal} on:keypress={onKeyPress} style="margin-left: 0.625rem;"
                           placeholder="새로운 목표를 세워보세요!"/>
                </div>
            </Box>
        </div>
    {/if}
</Box>

<style lang="scss">
  .title {
    margin: 0 16.813rem 0.594rem 0;
    font-size: 1.5rem;
    font-weight: 500;
    color: #dce6f2;
  }

  .expand:hover {
    cursor: pointer;
  }

  .top-div:hover {
    cursor: pointer;
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
    width: 6rem;
    text-align: center;
    font-size: 1.5rem;
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

  .img:hover {
    cursor: pointer;
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