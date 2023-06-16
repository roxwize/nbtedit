<script>
    import RecursiveTag from "../RecursiveTag.svelte";

    export let update;
    export let data;
</script>

<h3 class="mt-3"><a href={data.reference ? "https://minecraft.fandom.com/wiki/" + data.reference : ""}>{data.name}</a></h3>
<p>{data.description}</p>
{#each Object.entries(data.tags) as tag}
    <RecursiveTag entry={tag} updateFunc={update} />
{/each}