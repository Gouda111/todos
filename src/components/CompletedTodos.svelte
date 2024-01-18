<script>
    import { afterUpdate, beforeUpdate, createEventDispatcher } from "svelte";
    import { flip } from "svelte/animate";
    import { fly, scale,crossfade } from "svelte/transition";
    let listDivHeight, autoscroll;
    export let todos = null;
    export let loading = false;
    export let error = "An Error Occured";
    let previousTodos = todos;
    const [send,recieve] =crossfade({
        duration:300,
        fallback(node){
            return scale(node,{start:0.5,duration:300})
        }
    })
    $: {
        // if the user added new todo scroll to it's position
        autoscroll =
            todos &&
            previousTodos &&
            previousTodos.length <
            todos.length;
        previousTodos =todos && todos;
    }
    // Create custom events
    const dispatch = createEventDispatcher();

    // hide/show todo list
    function handleToggleTodo(id, value) {
        dispatch("toggletodo", {
            id,
            value,
        });
    }

    beforeUpdate(() => {
        if (listDivHeight) {
            // console.log('before update called',listDivHeight.offsetHeight);
        }
    });
    // scroll to newly added todo function
    afterUpdate(() => {
        if (autoscroll) {
            console.log(listDivHeight.scrollHeight);
            listDivHeight.scrollTo(0, listDivHeight.scrollHeight);
            autoscroll = false;
        }
    });
</script>

{#if loading}
<h1 class="text-black text-lg text-center">Loading....</h1>
{:else if error}
    <div class="error">Error: {error}</div>
{:else}
    <!-- Todo List -->
    {#if todos}
        {#if todos.length === 0}
            <div class="flex justify-center max-h-60 w-4xl">
                <p class="text-2xl text-red-500 font-bold">
                    No todos are available
                </p>
            </div>
        {:else}
            <h2 class="text-sm text-[#3B3333] mb-4">
                You Have Total {#key todos.length}<strong in:fly={{y:-10}}>{todos.length}</strong>{/key} Todos
            </h2>
        {/if}

        <div
            class="max-h-60 overflow-auto scrollbar"
            bind:this={listDivHeight}
        >
            <ul class="">
                {#each todos as { todo, completed, id }, index (id)}
                        <li
                            animate:flip={{duration:300}}
                            class="text-lg flex justify-between to-do my-3 shadow-[#00000029] border-[#DEDEDE] shadow-sm p-2 {completed
                                ? 'line-through opacity-90'
                                : ''}"
                            
                        >
                            <label for={todo} class="flex items-start gap-2"
                            in:recieve={{key:id}}
                            out:send={{key:id}}
        
                            >
                                <input
                                    type="checkbox"
                                    checked={completed}
                                    class="checkbox checkbox-success checkbox-sm"
                                    readonly
                                    disabled
                                />
                                <span
                                    id={todo}
                                    class="text-[#000000] font-semibold text-sm break-words whitespace-break-spaces"
                                    >{todo}</span
                                >
                            </label>

                            <button
                                type="button"
                                on:click={(event) => {
                                    event.currentTarget.checked = completed;
                                    handleToggleTodo(id, !completed);
                                }}
                                title="Revert"
                                class=""
                            >
                                <img
                                    class="object-cover w-5 h-5 z-[2] hidden"
                                    src={"images/previous.png"}
                                    alt="delete button"
                                />
                            </button>
                        </li>
                {/each}
            </ul>
        </div>
    {/if}

{/if}

<style lang="scss">
    :global(.scrollbar::-webkit-scrollbar-track) {
        background-color: #f5f5f5;
        -webkit-box-shadow: inset 0 0 6px rgba(0, 0, 0, 0.3);
    }
    :global(.scrollbar::-webkit-scrollbar-thumb) {
        background-clip: padding-box;
        background-color: #424242;
        border: 1px solid #fff;
        border-radius: 6.25px;
    }
    :global(.scrollbar::-webkit-scrollbar) {
        background-color: #f5f5f5;
        width: 0px;
    }
    :global(.scrollbar::-webkit-scrollbar:horizontal) {
        height: 10px;
    }
    :global(.to-do) {
        &:hover {
            img {
                display: block;
            }
        }
    }
</style>
