<script lang="ts">
    import checkedSrc from "$static/checked.svg?url";
    import uncheckedSrc from "$static/unchecked.svg?url";
    import closeSrc from "$static/close.svg?url";
    import plusSrc from "$static/plus.svg?url";

    export let todoList = [], goal, status;
    export let AlterChecked;
    export let _AlterChecked;
    export let DeleteTodo;
    export let OnKeyPress;
</script>

<div class="container --scroll">
    {#each todoList as goal}
        {#if !goal.completed}
            <div class="goal">
                <img src={uncheckedSrc} on:click={AlterChecked(goal)}/>
                <div on:click={() => status = goal.goal} class="text"
                     style="margin-left: 0.625rem; width: 75%; font-size: 1rem;">{goal.goal}</div>
                <img class="close" src={closeSrc} on:click|stopPropagation={DeleteTodo(goal.todo_id)}/>
            </div>
        {/if}
    {/each}
    {#each todoList as goal}
        {#if goal.completed}
            <div class="goal">
                <img src={checkedSrc} on:click={_AlterChecked(goal)}/>
                <div style="margin-left: 0.625rem; width: 75%; font-size: 1rem;">{goal.goal}</div>
                <img class="close" src={closeSrc} on:click|stopPropagation={DeleteTodo(goal.todo_id)}/>
            </div>
        {/if}
    {/each}
</div>

<div style="display: flex; margin-top: 0.625rem">
    <img src={plusSrc}/>
    <input bind:value={goal} on:keypress={OnKeyPress} style="margin-left: 0.625rem; width: 100%"
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