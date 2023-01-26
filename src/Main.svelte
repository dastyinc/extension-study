<script lang="ts">
    import {Env} from "@dastyinc/kit-panel";
    import App from "./App.svelte";
</script>

<Env>
    <App {...$$props}/>
</Env>
