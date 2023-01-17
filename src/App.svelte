<script lang="ts">
    import {Box, Modal} from '@dastyinc/kit-panel';
    import {slide} from "svelte/transition";
    import {getContext, onMount} from "svelte";

    import moreSrc from "$static/more.svg?url";
    import checkboxSrc from "$static/checkbox.svg?url";
    import checkedSrc from "$static/checked.svg?url";
    import uncheckedSrc from "$static/unchecked.svg?url";
    import closeSrc from "$static/close.svg?url";
    import plusSrc from "$static/plus.svg?url";

    const {api} = getContext('utils');
    const {user_id, user_name} = getContext('account');
    const channel = getContext('channel');

    export let showTodo = false;
    export let showStudyModal = false;

    let todos, todoList = [], todoListCompleted = [];
    let goal = "", completed = false;
    let _completed = 0;

    $: {
        todoList.forEach(todo => {
            if (todo.completed) {
                _completed += 1;
            }
        });
    }

    onMount(() => {
        getTodoList();
    })

    onMount(() => ws(channel).conn(({message, uid, send}) => {
        sendWs = send;
        wsStore(message, 'TODO_UPDATE').subscribe((msg) => updateTodoList(msg.id));
    }))
    $: sendTodoListUpdate = throttle(() => {
        sendWs?.({type: 'TODO_UPDATE'});
    });

    async function getTodoList() {
        todos = await api(`/todoList/todo/${channel}`)
        todoList = todos.todos;
    }

    async function deleteTodo(id) {
        await api(`/todoList/todo/${id}`, {}, 'DELETE')
        getTodoList();
        sendTodoListUpdate()
    }

    async function alterChecked(_goal) {
        goal = _goal.goal;
        completed = true;
        deleteTodo(_goal.todo_id)
        api('/todoList/todo', {user_id, user_name, channel_id: channel, goal, completed}).then(() => {
        }).catch(({error}) => {
        })
        goal = "";
        completed = false;
        getTodoList();
        sendTodoListUpdate()
    }

    function sendTodo() {
        api('/todoList/todo', {user_id, user_name, channel_id: channel, goal, completed}).then(() => {
            sendTodoListUpdate()
        }).catch(({error}) => {
        })
        getTodoList();
        goal = "";
    }

    function onKeyPress(e) {
        if (e.charCode === 13) {
            sendTodo();
        }
    }

    async function updateTodoList() {
        await getTodoList()
    }
</script>

<div style="display: flex; border-bottom: 1px solid rgba(255, 255, 255, 0.5);">
    <div class="title">Study</div>
</div>

<div style="float: right; margin-top: -2.4rem; margin-right: 0.444rem">
    <img src={moreSrc} on:click={() => showStudyModal = !showStudyModal}/>
</div>

<Box background="#ffffff" style="margin-top: 1.219rem; padding: 0.938rem 1.25rem 0.812rem 1.25rem;">
    <div style="display: flex; justify-content: space-between;">
        <div class="top-div" on:click={() => showTodo = !showTodo}>
            <div class="name" style="color: #ffffff;">Study</div>
            <div class="status" style="color: black; margin-top: 0.625rem">TOEFL 공부 중</div>
            <div style="display: flex; margin: 0.625rem 0 0.875rem 0">
                <img src={checkboxSrc}/>
                <div style="color: black; line-height: 1.5rem; margin-left: 0.312rem">{_completed}
                    / {todoList.length}</div>
            </div>
        </div>
        <div class="circle">
            <div class="circle-text">test</div>
        </div>
    </div>

    {#if showTodo}
        <div transition:slide>
            <Box background="#28222d" shadow style="padding: 0.625rem; margin: 20px 0 0.438rem 0;">
                <div class="name" style="background-color: white; color: #28222d">Todo-List</div>
                {#each todoList as goal}
                    {#if !goal.completed}
                        <div class="goal">
                            <img src={goal.completed ? checkedSrc : uncheckedSrc}
                                 on:click={goal.completed ? null : alterChecked(goal)}/>
                            <div style="margin-left: 0.625rem; width: 75%; font-size: 1rem;">{goal.goal}</div>
                            <img class="close" src={closeSrc} on:click|stopPropagation={deleteTodo(goal.todo_id)}/>
                        </div>
                    {/if}
                {/each}
                {#each todoListCompleted as goal}
                    <div class="goal">
                        <img src={goal.completed ? checkedSrc : uncheckedSrc}
                             on:click={goal.completed ? null : alterChecked(goal)}/>
                        <div style="margin-left: 0.625rem; width: 75%; font-size: 1rem;">{goal.goal}</div>
                        <img class="close" src={closeSrc} on:click|stopPropagation={deleteTodo(goal.todo_id)}/>
                    </div>
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

<Modal bind:showModal={showStudyModal}>
    <div style="width: 70vw;min-width: 600px;">
        <div style="display: flex;">
            <div class="name-bold">{user_name}</div>
            <div class="name-normal" style="line-height: 2.8rem">의</div>
        </div>
        <div class="name-normal">Study Dashboard</div>

        <div style="display: flex;">
            <div style="margin-right: 3.125rem;">
                <Box background="#f8f8f8" style="width: 24.813rem; height: 25.625rem;">
                    <div style="color: black;">1</div>
                </Box>

                <Box background="#28222d" style="width: 24.813rem; height: 16.438rem; margin-top: 3.125rem;">
                    <div style="color: black;">2</div>
                </Box>
            </div>

            <div>
                <Box background="#28222d" style="width: 24.813rem; height: 21rem;">
                    <div style="color: black;">3</div>
                </Box>

                <Box background="#f8f8f8" style="width: 24.813rem; height: 21rem; margin-top: 3.125rem;">
                    <div style="color: black;">4</div>
                </Box>
            </div>
        </div>
    </div>
</Modal>

<style lang="scss">
  .title {
    margin: 0 16.813rem 0.594rem 0;
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

  .top-div:hover {
    cursor: pointer;
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
