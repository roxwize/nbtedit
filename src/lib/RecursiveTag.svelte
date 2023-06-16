<script>
    import Tag from "./Tag.svelte";

    export let entry;
    export let updateFunc;
    export let value;
    let c = !!entry[1].tags;
</script>

<Tag {entry} {updateFunc} bind:value input={!c}>
    {#if c && entry[1].type === "list"}
        {#each entry[1].tags as tag, i}
            <Tag entry={[null,tag]} updateFunc={function(e, v) {
                value[i] = {type:tag.type,val:v,parent:entry[1].name};
                updateFunc(e, value)
            }} color="light" classes="mt-2" />
        {/each}
    {:else}
    {/if}
</Tag>