<script>
    import { afterUpdate, beforeUpdate, createEventDispatcher} from "svelte";
let inputvalue; 

const dispatch = createEventDispatcher();

// CRUD operations for todo
function handleAddTodo() {
    if (!inputvalue) return;
    const isNotCancelled = dispatch(
        "addtodo",
        {
            title: inputvalue,
        },
        { cancelable: true },
    );
    if (isNotCancelled) {
        // Now console not run
        console.log("Dispatched add todo event");
    }
}
export const readonly='Hey brir read';
export function clearInput() {
    inputvalue = "";
}


</script>

<div class="flex justify-around">
    <div class="mt-2 my-5">
        <form class="add-todo-form flex gap-5" on:submit={handleAddTodo}>
            <input type="text" placeholder="New Todo" class="input input-bordered text-slate-800 max-w-[800px]" bind:value={inputvalue}/>
            <button
                type="submit"
                class="btn btn-error bg-[#de001b]">
                Add
            </button>
        </form>
    </div>
</div>