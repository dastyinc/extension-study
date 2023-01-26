<script lang="ts">
  import { Box, Modal } from "@dastyinc/kit-panel";
  import { slide } from "svelte/transition";
  import { getContext, onMount } from "svelte";

  import checkboxSrc from "$static/checkbox.svg?url";
  import checkedSrc from "$static/checked.svg?url";
  import uncheckedSrc from "$static/unchecked.svg?url";
  import closeSrc from "$static/close.svg?url";
  import plusSrc from "$static/plus.svg?url";
  import modalSrc from "$static/modal.png?url";
  import Chart from "$lib/Chart.svelte";
  import Ranking from "$lib/Ranking.svelte";
  import Timer from "$lib/Timer.svelte";
  import TodoList from "$lib/TodoList.svelte";

  const { api, ws, wsStore, throttle } = getContext("utils");
  const { user_id, user_name } = getContext("account");
  const channel = getContext("channel");

  export let showTodo = false;
  export let showStudyModal = false;

  let hours = "00", minutes = "00", seconds = 0;
  let todos, todoList = [];
  let goal = "", completed = false;
  let _completed = 0;
  let sendWs;
  let play = false;
  let status = "Studying...";
  let showPlayPause = false;
  let time = 0;
  let timeObj;
  let timeArr = [];
  let time_id;
  let intervalId;

  let weeklyStudy = [
    { x: "0", y: "3" },
    { x: "1", y: "4" },
    { x: "2", y: "4" },
    { x: "3", y: "5" },
    { x: "4", y: "8" },
    { x: "5", y: "3" },
    { x: "6", y: "9" },
  ];

  $: console.log(time)

  $: {
    _completed = 0;
    todoList.forEach((todo) => {
      if (todo.completed) {
        _completed += 1;
      }
    });
  }

  onMount(() => {
    getTodoList();
    if(!play){
      getTime();
      initTimeArr();
    }
  });

  onMount(() =>
    ws(channel).conn(({ message, uid, send }) => {
      sendWs = send;
      wsStore(message, "TODO_UPDATE").subscribe((msg) =>
        updateTodoList(msg.id)
      );
    })
  );
  $: sendTodoListUpdate = throttle(() => {
    sendWs?.({ type: "TODO_UPDATE" });
  });

  async function getTodoList() {
    todos = await api(`/todoList/todo/user/${user_id}`);
    todoList = todos.todos;
  }

  async function deleteTodo(id) {
    await api(`/todoList/todo/${id}`, {}, "DELETE");
    getTodoList();
    sendTodoListUpdate();
  }

  async function alterChecked(_goal) {
    deleteTodo(_goal.todo_id);
    api("/todoList/todo", {
      user_id,
      user_name,
      channel_id: channel,
      goal: _goal.goal,
      completed: true,
    })
      .then(() => {})
      .catch(({ error }) => {});
    getTodoList();
    sendTodoListUpdate();
  }

  async function _alterChecked(_goal){
    deleteTodo(_goal.todo_id);
    api("/todoList/todo", {
      user_id,
      user_name,
      channel_id: channel,
      goal: _goal.goal,
      completed: false,
    })
      .then(() => {})
      .catch(({ error }) => {});
    getTodoList();
    sendTodoListUpdate();
  }

  function sendTodo() {
    api("/todoList/todo", {
      user_id,
      user_name,
      channel_id: channel,
      goal,
      completed,
    })
      .then(() => {
        sendTodoListUpdate();
      })
      .catch(({ error }) => {});
    getTodoList();
    goal = "";
  }

  function onKeyPress(e) {
    if (e.charCode === 13 && goal !== "") {
      sendTodo();
    }
  }

  async function updateTodoList() {
    await getTodoList();
  }

  function handleMouseUp(){
    showPlayPause = true;
  }

  async function getTime() {
    timeObj = await api(`/timer/time/${user_id}`);
    timeArr = timeObj.time;
    time = timeArr[0]?.time;
  }

  async function startTimer() {
    await getTime();
    time = timeArr[0].time;
    time_id = timeArr[0].time_id;
    clearInterval(intervalId);
    intervalId = setInterval(() => {
      if (play) time++;
    }, 1000);
  }

  async function stopTimer() {
    await api(`/timer/time/${time_id}`, {}, "DELETE");
    await api(`/timer/time`, { user_id, user_name, channel_id: channel, time });
  }

  async function initTimeArr() {
    getTime();
    if (timeArr.length === 0) {
      await api(`/timer/time`, {
        user_id,
        user_name,
        channel_id: channel,
        time: 0,
      });
    }
  }
</script>

<div style="display: flex; border-bottom: 1px solid rgba(255, 255, 255, 0.5);">
  <div class="title">Study</div>
</div>

<div style="float: right; margin-top: -2.4rem; margin-right: 0.444rem">
  <img
    src={modalSrc}
    class="expand"
    on:click={() => (showStudyModal = !showStudyModal)}
  />
</div>

<Box
  background="#ffffff"
  style="margin-top: 1.219rem; padding: 0.938rem 1.25rem 0.812rem 1.25rem;"
>
  <div style="display: flex; justify-content: space-between;">
    <div class="top-div" on:click={() => (showTodo = !showTodo)}>
      <div class="name" style="color: #ffffff;">Study</div>
      <div class="status" style="color: black; margin-top: 0.625rem">{status}</div>
      <div style="display: flex; margin: 0.625rem 0 0.875rem 0">
        <img src={checkboxSrc} />
        <div style="color: black; line-height: 1.5rem; margin-left: 0.312rem">
          {_completed} / {todoList.length}
        </div>
      </div>
    </div>
    <div class="circle" on:mouseup={handleMouseUp}>
      <div class="circle-text">{hours} : {minutes}</div>
    </div>
  </div>

  {#if showTodo}
    <div transition:slide>
      <Box
        background="#28222d"
        shadow
        style="padding: 0.625rem; margin: 20px 0 0.438rem 0;"
      >
        <div class="name" style="background-color: white; color: #28222d">
          Todo-List
        </div>
        {#each todoList as goal}
          {#if !goal.completed}
            <div class="goal">
              <img
                class="img"
                src={uncheckedSrc}
                on:click={alterChecked(goal)}
              />
              <div on:click={() => status = goal.goal} class="text" style="margin-left: 0.625rem; width: 75%; font-size: 1rem;">
                {goal.goal}
              </div>
              <img
                class="close img"
                src={closeSrc}
                on:click|stopPropagation={deleteTodo(goal.todo_id)}
              />
            </div>
          {/if}
        {/each}
        {#each todoList as goal}
          {#if goal.completed}
            <div class="goal">
              <img class="img" src={checkedSrc} on:click={_alterChecked(goal)}/>
              <div style="margin-left: 0.625rem; width: 75%; font-size: 1rem;">
                {goal.goal}
              </div>
              <img
                class="close img"
                src={closeSrc}
                on:click|stopPropagation={deleteTodo(goal.todo_id)}
              />
            </div>
          {/if}
        {/each}
        <div style="display: flex; margin-top: 0.625rem">
          <img src={plusSrc} />
          <input
            bind:value={goal}
            on:keypress={onKeyPress}
            style="margin-left: 0.625rem;"
            placeholder="새로운 목표를 세워보세요!"
          />
        </div>
      </Box>
    </div>
  {/if}
</Box>

<Modal
  bind:showModal={showStudyModal}
  style="width: fit-content; height: fit-content; padding: 1.375rem 2.375rem 2.375rem 2.375rem"
>
  <div style="display: flex;">
    <div class="name-bold">{user_name}</div>
    <div class="name-normal" style="line-height: 2.2rem">의</div>
  </div>
  <div class="name-normal" style="margin-bottom: 1.438rem;">
    Study Dashboard
  </div>

  <div style="display: flex;">
    <div style="margin-right: 1.375rem;">
      <Box
        background="#f8f8f8"
        style="width: 18.813rem; height: 19.438rem; padding: 1.25rem"
      >
        <div class="name" style="color: #ffffff;">Today</div>
        <Timer
          bind:hours
          bind:minutes 
          bind:seconds 
          bind:play 
          bind:status 
          bind:time 
          bind:time_id
          startTimer={startTimer}
          stopTimer={stopTimer}
          getTime={getTime}
        />
      </Box>

      <Box
        background="#28222d"
        style="width: 18.813rem; height: 12.438rem; margin-top: 1.375rem; padding: 1.25rem"
      >
        <div class="name" style="background-color: white; color: #28222d">
          Weekly
        </div>
        <Chart data={weeklyStudy} style="margin-top: 1.25rem; height: 9.2rem" />
      </Box>
    </div>

    <div>
      <Box
        background="#28222d"
        style="width: 18.813rem; height: 15.938rem; padding: 1.25rem"
      >
        <div class="name" style="background-color: white; color: #28222d">
          Todo-List
        </div>
        <TodoList
          bind:todoList
          bind:goal
          bind:status
          AlterChecked={alterChecked}
          _AlterChecked={_alterChecked}
          DeleteTodo={deleteTodo}
          OnKeyPress={onKeyPress}
        />
      </Box>

      <Box
        background="#f8f8f8"
        style="width: 18.813rem; height: 15.938rem; margin-top: 1.375rem; padding: 1.25rem"
      >
        <div class="name" style="color: #ffffff;">Ranking</div>
        <Ranking />
      </Box>
    </div>
  </div>
</Modal>

<style lang="scss">
  .name-bold {
    color: #28222d;
    font-family: NotoSansKR;
    font-size: 1.5rem;
    font-weight: bold;
  }

  .name-normal {
    color: #28222d;
    font-family: NotoSansKR;
    font-size: 1.125rem;
    font-weight: normal;
  }

  .container {
    margin-top: 0.4rem;
    max-height: 12.5rem;
    overflow-y: scroll;
  }

  .title {
    margin: 0 16.813rem 0.594rem 0;
    font-size: 1.5rem;
    font-weight: 500;
    color: #dce6f2;
  }

  .text:hover{
    cursor: pointer;
  }

  .expand:hover {
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
    width: 6rem;
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

  .img:hover {
    cursor: pointer;
  }
</style>
