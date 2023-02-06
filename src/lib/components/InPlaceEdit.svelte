<script lang="ts">
    import closeSrc from "$static/close.svg?url";
    import editSrc from "$static/edit.svg?url";
    
    export let text, id;
    export let deleteTodo = () => {};
    export let editTodo = () => {};

    let edit = false;

    function onKeyPress(e){
        if (e.charCode === 13 && text !== "") {
            editTodo(text, id);
            edit = false
        }
    }
</script>

<div class="text">
    {#if !edit}
        <div style="margin-left: 0.625rem; width: 100%; font-size: 1rem;">{text}</div>
    {:else}
        <input bind:value={text} on:click|stopPropagation on:keypress={onKeyPress} autofocus/>
    {/if}
    <div style="display: flex;">
        {#if !edit}
            <img class="edit" src={editSrc} on:click|stopPropagation={() => edit = true}>
        {/if}
        <img class="close" src={closeSrc} on:click|stopPropagation={deleteTodo(id)}/>
    </div>
</div>

<style lang="scss">
    img{
        width: 1.6rem;
        height: 1.6rem;
    }
    img:hover{
        cursor: pointer;
    }
    .text{
        display: flex;
        justify-content: space-between;
    }
    .edit{
        opacity: 0;
        width: 1rem;
        height: 1rem;
    }
    .close{
        opacity: 0;
    }
    .text:hover .edit, .text:hover .close{
        opacity: 1;
    }
    input {
        background-color: transparent;
        border: none;
        color: white;
        width: 90%;
        margin-left: 0.625rem; 
    }
    input::placeholder {
        color: white;
        opacity: 0.5;
    }
</style>