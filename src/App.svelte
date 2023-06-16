<script>
    import {Styles, TabContent, TabPane} from "sveltestrap";
    import Header from "./lib/Header.svelte";

    import NBTBasic from "./lib/nbt/NBTBasic.svelte";

    import * as Entity from "./lib/nbt/Entity.json";
    import * as WrittenBook from "./lib/nbt/WrittenBook.json";

    let table = {};
    $: generated = generate(table);

    function getSuffix(s) {
        switch (s) {
            case "boolean":
            case "byte":
                return "b";
            case "short":
                return "s";
            case "long":
                return "l";
            case "float":
                return "f";
            case "double":
                return "d";
            case "int":
            case "string":
            default:
                return "";
        }
    }

    function generate(t) {
        if (Object.keys(t).length <= 0) return "{}";
        let str = "{";
        for (const [key, value] of Object.entries(t)) {
            let v;
            if (typeof(value.val) === "object") {
                v = "[";
                for (let i = 0; i < value.val.length; i++) {
                    // Oh my fucking god
                    v += value.val[i].val + getSuffix(value.val[i].type) + ","
                }
                v = v.slice(0, -1);
                v += "]";
            }
            else if (value.type === "string") v = "\"" + value.val + "\"";
            else if (value.type === "boolean") v = value.val + "b";
            else v = value.val + getSuffix(value.type);
            str += "\""+ key +"\":" + v + ",";
        }
        str = str.slice(0, -1);
        return str + "}";
    }

    function update(entry, value) {
        let n = entry.name;
        if (typeof(value) === "object") {
            n = value[0].parent;
            entry.name = n;
        }
        table[n] = {
            ...entry,
            val: value,
        };
        let d;
        if (entry.type === "boolean" && !entry.default) d = 0;
        else if (!entry.default) d = "";
        else d = entry.default;
        // have arrays filled with default values remove the array
        if (value == d && table[entry.name]) { delete table[entry.name] }
    }
</script>

<Styles/>

<Header/>
<main class="p-4">
    <h1>NBTEdit</h1>
    <p>NBTEdit is an online utility that lets you generate NBT data structures for use in Minecraft.<br/><br/>Information sourced from <a href="https://minecraft.fandom.com/">the Minecraft Wiki</a>.</p>
    <TabContent>
        <TabPane tabId="entity" tab="Entity" active>
            <NBTBasic {update} data={Entity} />
        </TabPane>
        <TabPane tabId="book" tab="Written Book">
            <NBTBasic {update} data={WrittenBook} />
        </TabPane>
    </TabContent>
    <pre class="text-light bg-dark mt-4 p-3" id="output">{generated}</pre>
</main>