<script>
    import {Alert, Card, CardBody} from "sveltestrap";

    export let color = "white";
    export let classes = "mb-3";
    export let valid = "";
    export let input = true;
    export let entry = [null,{}];
    export let updateFunc = function() {

    };
    export let value = entry[1].default || "";
    if (entry[1].type === "list" && entry[1].tags) {
        value = [];
        for (let i = 0; i < entry[1].tags.length; i++) {
            value[i] = {
                type: entry[1].tags[i].type,
                val: def(entry[1].tags[i].type),
                parent: entry[1].name
            };
        }
    }
    let inputType, minimum, maximum;
    if (input) {
        switch (entry[1].type) {
            case "boolean":
                inputType = "checkbox";
                break;
            case "byte":
                inputType = "number";
                minimum = -128;
                maximum = 127;
                break;
            case "short":
                inputType = "number";
                minimum = -32768;
                maximum = 32767;
                break;
            case "int":
                inputType = "number";
                minimum = -2147483648;
                maximum = 2147483647;
                break;
            case "long":
                inputType = "number";
                minimum = -9223372036854775808;
                maximum = 9223372036854775807;
                break;
            case "float":
            case "double":
                inputType = "number";
                break;
            case "string":
            default:
                inputType = "text";
                break;
        }
    }

    function def(t) {
        if (t === "string") return "";
        else return "0";
    }

    function validate() {
        const e = document.getElementById("input-" + entry[1].name);
        if (entry[1].type !== "boolean") {
            if ((e.value.length < 1 || e.type === "text") && !entry[1].required) return;
            if (e.value.length < 1 && entry[1].required) {
                valid = "This field is mandatory"
            } else if (e.value < minimum) {
                valid = "The " + entry[1].type + " value is lower than the minimum (" + minimum + ")";
            } else if (e.value > maximum) {
                valid = "The " + entry[1].type + " value is higher than the maximum (" + maximum + ")";
            } else if (entry[1].type === "int" && e.value.indexOf(".") !== -1) {
                valid = "Integer values cannot contain decimals";
            }
            else valid = "";
        }
    }
    // validate();
</script>

<Card class={classes} {color}>
    <CardBody>
        <strong>{entry[1].name} [{entry[1].type}]</strong><br/>
        {#if entry[1].description}
            <span>{@html entry[1].description}</span><br/>
        {/if}
        {#if input}
            <input
                    type={inputType}
                    min={minimum}
                    max={maximum}
                    placeholder={entry[1].example || entry[1].type}
                    name={entry[1].name}
                    id="input-{entry[1].name}"
                    value={entry[1].default || ""}
                    on:input={validate}
                    on:change={
                    function(e) {
                        if (!(entry[1].type === "list" && entry[1].tags)) {
                            if (entry[1].type === "boolean") {
                                value = e.target.checked ? 1 : 0;
                            } else {
                                value = e.target.value;
                            }
                        }
                        updateFunc(entry[1], value);
                    }
                }
            />
        {/if}
        <slot></slot>
        {#if valid.length > 0}
            <Alert class="mt-4" color="danger">{valid}!</Alert>
        {/if}
    </CardBody>
</Card>