<script lang="ts">
    import {Modal, Box} from "@dastyinc/kit-panel";
    import {getContext} from "svelte";
    import Timer from "./Timer.svelte";
    import Chart from "./Chart.svelte";
    import TodoList from "./TodoList.svelte";
    import Ranking from "./Ranking.svelte";

    export let showStudyModal, play, status, time_id, clicked;
    export let todoList, goal;
    export let alterChecked = () => {
    };
    export let _alterChecked = () => {
    };
    export let deleteTodo = () => {
    };
    export let onKeyPress = () => {
    };
    export let resetTimer = () => {
        
    }

    const {user_name} = getContext("account");

    let weeklyStudy = [
        {x: "0", y: "3"},
        {x: "1", y: "4"},
        {x: "2", y: "4"},
        {x: "3", y: "5"},
        {x: "4", y: "8"},
        {x: "5", y: "3"},
        {x: "6", y: "9"},
    ];
</script>

<Modal bind:showModal={showStudyModal}
       style="width: fit-content; height: fit-content; padding: 1.375rem 2.375rem 2.375rem 2.375rem">
    <div style="display: flex;">
        <div class="name-bold">{user_name}</div>
        <div class="name-normal" style="line-height: 2.2rem">의</div>
    </div>

    <div class="name-normal" style="margin-bottom: 1.438rem;">
        Study Dashboard
    </div>

    <div style="display: flex;">
        <div style="margin-right: 1.375rem;">
            <Box background="#f8f8f8" style="width: 18.813rem; height: 19.438rem; padding: 1.25rem">
                <div class="name" style="color: #ffffff;">Today</div>
                <Timer bind:play bind:status bind:clicked resetTimer={resetTimer}/>
            </Box>

            <Box background="#28222d"
                 style="width: 18.813rem; height: 12.438rem; margin-top: 1.375rem; padding: 1.25rem">
                <div class="name" style="background-color: white; color: #28222d">Weekly</div>
                <Chart data={weeklyStudy} style="margin-top: 1.25rem; height: 9.2rem"/>
            </Box>
        </div>

        <div>
            <Box background="#28222d" style="width: 18.813rem; height: 15.938rem; padding: 1.25rem">
                <div class="name" style="background-color: white; color: #28222d">Todo-List</div>
                <TodoList bind:todoList bind:goal bind:status
                          AlterChecked={alterChecked} _AlterChecked={_alterChecked}
                          DeleteTodo={deleteTodo} OnKeyPress={onKeyPress}/>
            </Box>

            <Box background="#f8f8f8"
                 style="width: 18.813rem; height: 15.938rem; margin-top: 1.375rem; padding: 1.25rem">
                <div class="name" style="color: #ffffff;">Ranking</div>
                <Ranking/>
            </Box>
        </div>
    </div>
</Modal>

<style lang="scss">
  .name-bold {
    color: #28222d;
    font-size: 1.5rem;
    font-weight: bold;
  }

  .name-normal {
    color: #28222d;
    font-size: 1.125rem;
    font-weight: normal;
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
</style>