<script>
import RecursiveTag from "../RecursiveTag.svelte";

export let update;

import * as e from "./Entity.json";
</script>

{#each Object.entries(e.tags) as tag}
    <RecursiveTag entry={tag} updateFunc={update} />
{/each}