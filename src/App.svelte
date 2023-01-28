<script lang="ts">
    import {getContext, onDestroy, onMount, setContext} from "svelte";
    import StudyModal from "$lib/StudyModal.svelte";
    import StudyPanel from "$lib/StudyPanel.svelte";
    import Portal from "svelte-portal";
    import {writable} from "svelte/store";
    import timeSrc from "$static/clock.svg?url";

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
    let nowHour, nowMinutes, nowSeconds, studyTime = writable(0);
    
    setContext('studyTime', $studyTime);

    $: {
        if(play){
            $studyTime = time + nowHour*3600 + nowMinutes*60 + nowSeconds - startTime;
        }
    }

    setInterval(() => {
        let now = new Date();
        nowHour = now.getHours();
        nowMinutes = now.getMinutes();
        nowSeconds = now.getSeconds();
    }, 1000);

    export let users = {};

    onMount(() => {
        getTodoList();
        getTimer();
        initTimer();
        ws(channel).conn(({message, uid, send}) => {
            sendWs = send;
            wsStore(message, "TODO_UPDATE").subscribe((msg) => updateTodoList(msg.id));
        });
    });

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

    async function getTimer(){
        timeObj = await api(`/timer/time/${user_id}`);
        time = timeObj.time[0].time;
        time_id = timeObj.time[0].time_id;
    }

    async function initTimer(){
        await getTimer();
        if(timeObj.time.length === 0){
            await api('/timer/time', { user_id, user_name, channel_id: channel, time: 0, startTime: 0 });
        }
    }

    async function startTimer(){
        await getTimer();
        startTime = nowHour*3600 + nowMinutes*60 + nowSeconds;
        await api(`/timer/time/${time_id}`, { time, startTime }, 'PUT');
    }
    
    async function stopTimer(){
        await api(`/timer/time/${time_id}`, { time: time + $studyTime, startTime }, 'PUT');
    }
</script>

{#each Object.keys(users) as user}
    {#if users[user].extensionRegion}
        <Portal target={users[user].extensionRegion}>
            <div class="overhead-timer">
                <img src={timeSrc} class="time-icon"/>
                <div class="time-text"></div>
            </div>
        </Portal>
    {/if}
{/each}

<StudyPanel bind:showStudyModal bind:showTodo bind:status bind:_completed bind:todoList bind:goal
            alterChecked={alterChecked} _alterChecked={_alterChecked} deleteTodo={deleteTodo} onKeyPress={onKeyPress}/>

<StudyModal bind:showStudyModal bind:play
            bind:status bind:time_id bind:todoList bind:goal bind:clicked
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