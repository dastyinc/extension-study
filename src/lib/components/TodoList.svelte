<script lang="ts">
    import checkedSrc from "$static/checked.svg?url";
    import uncheckedSrc from "$static/unchecked.svg?url";
    import closeSrc from "$static/close.svg?url";
    import plusSrc from "$static/plus.svg?url";
    import InPlaceEdit from "./InPlaceEdit.svelte";

    export let todoList = [], goal, status;
    export let editTodo = () => {
    };
    export let alterChecked = () => {
    };;
    export let _alterChecked = () => {
    };;
    export let deleteTodo = () => {
    };;
    export let onKeyPress = () => {
    };;
</script>

<div class="container --scroll">
    {#each todoList as goal}
        {#if !goal.completed}
            <div class="goal">
                <img src={uncheckedSrc} on:click={alterChecked(goal)}/>
                <div on:click={() => status = goal.goal} class="text"
                     style="margin-left: 0.225rem; width: 100%; font-size: 1rem;">
                     <InPlaceEdit bind:text={goal.goal} bind:id={goal.todo_id} {editTodo} {deleteTodo}/>
                </div>
            </div>
        {/if}
    {/each}
    {#each todoList as goal}
        {#if goal.completed}
            <div class="goal">
                <img src={checkedSrc} on:click={_alterChecked(goal)}/>
                <div style="margin-left: 0.625rem; width: 75%; font-size: 1rem;">{goal.goal}</div>
                <img class="close" src={closeSrc} on:click|stopPropagation={deleteTodo(goal.todo_id)}/>
            </div>
        {/if}
    {/each}
</div>

<div style="display: flex; margin-top: 0.625rem">
    <img src={plusSrc}/>
    <input bind:value={goal} on:keypress={onKeyPress} style="margin-left: 0.625rem; width: 100%"
           placeholder="새로운 목표를 세워보세요!"/>
</div>

<style lang="scss">
  .container {
    margin-top: 0.4rem;
    max-height: 12.5rem;
    overflow-y: auto;
  }

  .text:hover {
    cursor: pointer;
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

  span {
    -webkit-user-select: none;
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

  img:hover {
    cursor: pointer;
  }
</style>