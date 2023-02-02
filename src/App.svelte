<script lang="ts">
    import {getContext, onMount, setContext} from "svelte";
    import StudyModal from "$lib/components/StudyModal.svelte";
    import StudyPanel from "$lib/components/StudyPanel.svelte";
    import Portal from "svelte-portal";
    import {writable} from "svelte/store";
    import timeSrc from "$static/clock.svg?url";

    export let users = {};

    const {api, ws, wsStore, throttle} = getContext("utils");
    const {user_id, user_name} = getContext("account");
    const channel = getContext("channel");

    let showTodo = false;
    let showStudyModal = false;
    let clicked = false;
    let todos, todoList = [];
    let goal = "", completed = false;
    let _completed = 0;
    let sendWs;
    let play = false;
    let status = "Studying...";
    let time, time_id, startTime, timeObj;
    let nowHour,
        nowMinutes,
        nowSeconds,
        studyTime = writable(0);
    let hours, minutes, seconds;
    let userStudyDict = {};
        
    setContext("studyTime", studyTime);

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
    $: {
        seconds = Math.floor($studyTime - hours * 3600 - minutes * 60);
        if (seconds < 10) {
            seconds = "0" + seconds;
        }
    }
    $: {
        if (play) {
            $studyTime =
                time + nowHour * 3600 + nowMinutes * 60 + nowSeconds - startTime;
        }
    }
    $: {
        if (play && clicked) startTimer();
        else if (clicked) stopTimer();
    }
    $: {
        _completed = 0;
        todoList.forEach((todo) => {
            if (todo.completed) {
                _completed += 1;
            }
        });
    }
    $: sendTodoListUpdate = throttle(() => {
        sendWs?.("TODO_UPDATE");
    });

    setInterval(() => {
        let now = new Date();
        nowHour = now.getHours();
        nowMinutes = now.getMinutes();
        nowSeconds = now.getSeconds();
    }, 1000);

    setInterval(() => {
        Object.keys(users).forEach(userId => async function(){
            let userStudyObj = await api(`/timer/time/${userId}`);
            if (userStudyObj.time.length !== 0){
                userStudyDict[userId] = {
                    startTime : userStudyObj.time[0].startTime,
                    time : userStudyObj.time[0].time,
                    isPaused : userStudyObj.time[0].isPaused,
                };
            }
        });
    }, 300000);

    onMount(() => {
        getTodoList();
        getTimer();
        initTimer();
        ws(channel).conn(({message, uid, send}) => {
            sendWs = send;
            wsStore(message, "TODO_UPDATE").subscribe((msg) =>
            updateTodoList(msg.id)
            );
        });
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
            .then(() => {
            })
            .catch(({error}) => {
            });
        getTodoList();
        sendTodoListUpdate();
    }

    async function _alterChecked(_goal) {
        deleteTodo(_goal.todo_id);
        api("/todoList/todo", {
            user_id,
            user_name,
            channel_id: channel,
            goal: _goal.goal,
            completed: false,
        })
            .then(() => {
            })
            .catch(({error}) => {
            });
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
        .catch(({error}) => {
        });
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

    async function getTimer() {
        timeObj = await api(`/timer/time/${user_id}`);
        if (timeObj.time.length !== 0) {
            time = timeObj.time[0].time;
            time_id = timeObj.time[0].time_id;
        }
    }

    async function initTimer() {
        await getTimer();
        if (timeObj.time.length === 0) {
            await api("/timer/time", {
                user_id,
                user_name,
                channel_id: channel,
                time: 0,
                startTime: 0,
                isPaused: !play
            });
        }
        $studyTime = time;
    }

    async function startTimer() {
        await getTimer();
        startTime = nowHour * 3600 + nowMinutes * 60 + nowSeconds;
        await api(`/timer/time/edit`, {time_id, time, startTime, isPaused: !play}, "PUT");
    }

    async function stopTimer() {
        await getTimer();
        await api(`/timer/time/edit`, {time_id, time: $studyTime, startTime, isPaused: !play}, "PUT");
    }

    async function resetTimer() {
        $studyTime = 0;
        await api(`/timer/time/edit`, {time_id, time: 0, startTime, isPaused: !play}, 'PUT');
        await getTimer();
    }

    function calcStudyTime(_time, _startTime){
        let _studyTime = _time + nowHour * 3600 + nowMinutes * 60 + nowSeconds - _startTime;
        let _hours = Math.floor(_studyTime / 3600);
        if (_hours < 10) {
            _hours = "0" + _hours;
        }
        let _minutes = Math.floor((_studyTime % 3600) / 60);
        if (_minutes < 10) {
            _minutes = "0" + _minutes;
        }
        let _seconds = Math.floor(_studyTime % 60);
        if (_seconds < 10) {
            _seconds = "0" + _seconds;
        }
        return _hours + ":" + _minutes + ":" + _seconds;
    }
</script>

{#each Object.keys(users) as user}
    {#if users[user].extensionRegion}
        <Portal target={users[user].extensionRegion}>x
            {#if !userStudyDict[user].isPaused}
                <div class="overhead-timer">
                    <img src={timeSrc} class="time-icon"/>
                    <div>{calcStudyTime(userStudyDict[user].time, userStudyDict[user].startTime)}</div>
                </div>
            {/if}
        </Portal>
    {/if}
{/each}

<StudyPanel
        bind:showStudyModal
        bind:showTodo
        bind:status
        bind:_completed
        bind:todoList
        bind:goal
        bind:play
        bind:clicked
        {alterChecked}
        {_alterChecked}
        {deleteTodo}
        {onKeyPress}
/>

<StudyModal
        bind:showStudyModal
        bind:play
        bind:status
        bind:time_id
        bind:todoList
        bind:goal
        bind:clicked
        {alterChecked}
        {_alterChecked}
        {deleteTodo}
        {onKeyPress}
        {resetTimer}
/>

<style lang="scss">
  .overhead-timer {
    display: flex;
    padding: 0.375rem 0.938rem 0.375rem 0.938rem;
    border-radius: 10px;
    background-color: rgba(255, 255, 255, 0.6);
    width: fit-content;
  }

  .time-icon {
    width: 1.5rem;
    height: 1.5rem;
    display: flex;
    align-items: center;
  }

  .time-text {
    color: #241f28;
    font-family: NotoSansKR;
    font-size: 1.25rem;
    margin-left: 0.625rem;
    display: flex;
    align-items: center;
  }
</style>
