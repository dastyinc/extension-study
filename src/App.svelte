<script lang="ts">
    import {getContext, onDestroy, onMount, setContext} from "svelte";
    import StudyModal from "$lib/StudyModal.svelte";
    import StudyPanel from "$lib/StudyPanel.svelte";
    import Portal from "svelte-portal";
    import {writable} from "svelte/store";
    import timeSrc from "$static/Play.svg?url";

    const {api, ws, wsStore, throttle} = getContext("utils");
    const {user_id, user_name} = getContext("account");
    const channel = getContext("channel");

    let showTodo = false;
    let showStudyModal = false;
    let clicked = false;
    let hours, minutes, seconds;
    let todos, todoList = [];
    let goal = "", completed = false;
    let _completed = 0;
    let sendWs;
    let play = false;
    let status = "Studying...";
    let time = writable(0);
    let timeObj;
    let timeArr = [];
    let time_id;
    let intervalId;

    export let users = {};
    setContext('time', time);

    onMount(() => {
        getTodoList();
        if (!play) {
            getTime();
            initTimeArr();
        }
        ws(channel).conn(({message, uid, send}) => {
            sendWs = send;
            wsStore(message, "TODO_UPDATE").subscribe((msg) => updateTodoList(msg.id));
        });
    });

    onDestroy(() => {
        // play = false;
        stopTimer();
        console.log("hello")
    })

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
        sendWs?.({type: "TODO_UPDATE"});
    });

    $: {
        hours = Math.floor($time / 3600);
        if (hours < 10) {
            hours = "0" + hours;
        }
    }

    $: {
        minutes = Math.floor($time / 60 - hours * 60);
        if (minutes < 10) {
            minutes = "0" + minutes;
        }
    }

    $: {
        seconds = Math.floor($time - hours * 3600 - minutes * 60);
        if (seconds < 10) {
            seconds = "0" + seconds;
        }
    }

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

    async function getTime() {
        timeObj = await api(`/timer/time/${user_id}`);
        timeArr = timeObj.time;
        $time = timeArr[0]?.time;
    }

    async function startTimer() {
        await getTime();
        $time = timeArr[0].time;
        time_id = timeArr[0].time_id;
        clearInterval(intervalId);
        intervalId = setInterval(() => {
            if (play) $time++;
        }, 1000);
    }

    async function stopTimer() {
        await api(`/timer/time/${time_id}`, {}, "DELETE");
        await api(`/timer/time`, {user_id, user_name, channel_id: channel, time: $time});
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

    async function resetTimer() {
        await getTime();
        $time = timeArr[0].time;
        time_id = timeArr[0].time_id;
        await api(`/timer/time/${time_id}`, {}, "DELETE");
        await api(`/timer/time`, {
            user_id,
            user_name,
            channel_id: channel,
            time: 0,
        });
        await getTime();
    }
</script>

{#each Object.keys(users) as user}
    {#if users[user].extensionRegion}
        <Portal target={users[user].extensionRegion}>
            {#if user === user_id}
                <div class="overhead-timer">
                    <img src={timeSrc} class="time-icon"/>
                    <div class="time-text">{hours}:{minutes}:{seconds}</div>
                </div>
            {/if}
        </Portal>
    {/if}
{/each}

<StudyPanel bind:showStudyModal bind:showTodo bind:status bind:_completed bind:todoList
            bind:hours bind:minutes bind:goal
            alterChecked={alterChecked} _alterChecked={_alterChecked} deleteTodo={deleteTodo} onKeyPress={onKeyPress}/>

<StudyModal bind:showStudyModal bind:play
            bind:status bind:time_id bind:todoList bind:goal bind:clicked
            startTimer={startTimer} stopTimer={stopTimer} getTime={getTime} resetTimer={resetTimer}
            alterChecked={alterChecked} _alterChecked={_alterChecked} deleteTodo={deleteTodo} onKeyPress={onKeyPress}/>

<style lang="scss">
    .overhead-timer{
        display: flex;
        padding: 0.125rem 0.938rem 0.313rem;
        border-radius: 10px;
        background-color: rgba(255, 255, 255, 0.6);
    }
    .time-icon{
        width: 1.5rem;
        height: 1.5rem;
    }
    .time-text{
        color: #241f28;
        font-family: NotoSansKR;
        font-size: 1.25rem;
    }
</style>