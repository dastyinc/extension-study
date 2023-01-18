<script lang="ts">
    export let todoList = [], todoListCompleted = [], goal;
    export let AlterChecked = () => null;
    export let DeleteTodo = () => null;
    export let OnKeyPress = () => null;

    import checkedSrc from "$static/checked.svg?url";
    import uncheckedSrc from "$static/unchecked.svg?url";
    import closeSrc from "$static/close.svg?url";
    import plusSrc from "$static/plus.svg?url";
</script>

<div class="container">
    {#each todoList as goal}
        {#if !goal.completed}
            <div class="goal">
                <img src={goal.completed ? checkedSrc : uncheckedSrc}
                        on:click={goal.completed ? null : AlterChecked(goal)}/>
                <div style="margin-left: 0.625rem; width: 75%; font-size: 1rem;">{goal.goal}</div>
                <img class="close" src={closeSrc} on:click|stopPropagation={DeleteTodo(goal.todo_id)}/>
            </div>
        {/if}
    {/each}
    {#each todoListCompleted as goal}
        <div class="goal">
            <img src={goal.completed ? checkedSrc : uncheckedSrc}
                    on:click={goal.completed ? null : AlterChecked(goal)}/>
            <div style="margin-left: 0.625rem; width: 75%; font-size: 1rem;">{goal.goal}</div>
            <img class="close" src={closeSrc} on:click|stopPropagation={DeleteTodo(goal.todo_id)}/>
        </div>
    {/each}
</div>

<div style="display: flex; margin-top: 0.625rem">
    <img src={plusSrc}/>
    <input bind:value={goal} on:keypress={OnKeyPress} style="margin-left: 0.625rem; width: 100%" placeholder="새로운 목표를 세워보세요!"/>
</div>

<style lang="scss">
    .container{
        margin-top: 0.4rem;
        max-height: 12.5rem;
        overflow-y: scroll;
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
</style>