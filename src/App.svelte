<script lang="ts">
  import { getContext, onMount } from "svelte";
  import StudyModal from "$lib/StudyModal.svelte";
  import StudyPanel from "$lib/StudyPanel.svelte";

  const { api, ws, wsStore, throttle } = getContext("utils");
  const { user_id, user_name } = getContext("account");
  const channel = getContext("channel");
  
  let showTodo = false;
  let showStudyModal = false;
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

  $: console.log(time, timeArr)

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
    await getTime();
    if (timeArr.length === 0) {
      await api(`/timer/time`, {
        user_id,
        user_name,
        channel_id: channel,
        time: 0,
      });
    }
  }

  async function resetTimer(){
    await api(`/timer/time/${time_id}`, {}, "DELETE");
    await api(`/timer/time`, { user_id, user_name, channel_id: channel, time: 0 });
  }
</script>

<StudyPanel
  bind:showStudyModal
  bind:showTodo
  bind:status
  bind:_completed
  bind:todoList
  bind:hours
  bind:minutes
  bind:goal

  alterChecked={alterChecked}
  _alterChecked={_alterChecked}
  deleteTodo={deleteTodo}
  onKeyPress={onKeyPress}
/>

<StudyModal
  bind:showStudyModal
  bind:hours
  bind:minutes
  bind:seconds
  bind:play
  bind:status
  bind:time
  bind:time_id
  bind:todoList
  bind:goal

  startTimer={startTimer}
  stopTimer={stopTimer}
  getTime={getTime}
  resetTimer={resetTimer}
  alterChecked={alterChecked}
  _alterChecked={_alterChecked}
  deleteTodo={deleteTodo}
  onKeyPress={onKeyPress}
/>