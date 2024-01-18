<svelte:options immutable={true} />

<script>
    import { onDestroy, onMount,tick } from "svelte";
    import { cubicIn } from "svelte/easing";
    import { blur, slide } from "svelte/transition";
    import CompletedTodos from "./CompletedTodos.svelte";
    import PendingTodos from "./PendingTodos.svelte";
    import AddNewTodo from "./AddNewTodo.svelte";
    let todos=null;
    let error='';
    let loading=false;
    let todoList;

    // Life cycle methods 
    onMount (()=>{
        loadTodo();
        // this part will run after onDestroy
        return(()=>{
            console.log('hey bro i am unmounting')
        });
    });
    
    onDestroy(()=>{
        console.log('hey broo destroyed');
    });

    function loadTodo(){
        loading=true;
        fetch('https://dummyjson.com/todos?limit=10')
        .then(async res => {
            if(res.ok){
                const promise=await res.json(); 
                todos=promise.todos;
            }else{
                throw new Error('An error has occured')
            }
        }
        );
        loading=false;
    }

    async function handleAddtodo(event) {
        event.preventDefault();
            todos = [
                ...todos,
                {
                    todo: event.detail.title,
                    completed: false,
                    id: todos.length + 1,
                },
            ];
            todoList.clearInput();
            // wait until dom manapulation is done
            await tick();
    }
    function handelremoveTodo(event) {
        const todoId = parseInt(event.detail.id);
        todos = todos.filter((item) => item.id !== todoId);
    }

    function handletoggleTodo(event) {
        todos = todos.map((item) => {
            if (item.id === event.detail.id) {
                return (item = { ...item, completed: event.detail.value });
            }
            return { ...item };
        });

        console.log(todos);
    }

</script>
<div class="add-todos">
    <AddNewTodo
    bind:this={todoList}
    on:addtodo={handleAddtodo}
    ></AddNewTodo>
</div>
<div class="to-do-list flex flex-col lg:flex-row lg:justify-between gap-10 lg:container lg:mx-auto mb-[100px]">
    <div class="pending-todos border border-[#E9E9E9] bg-[#FFFFFF] p-6 w-full lg:w-2/4"
        in:slide={{duration:500, delay:300, easing:cubicIn}}
        out:blur={{amount:10, duration:500}}
        on:introstart={()=>console.log('animation start')}
        on:introend={()=>console.log('animation end')}
        on:outrostart={()=>console.log('animation close start')}
        on:outroend={()=>console.log('animation close end')}
    >
        <h2 class="text-xl font-bold text[##3B3333]">Pending todos</h2>
        
        <PendingTodos 
        todos={todos && todos.filter((todo) => !todo.completed)}
        on:removetodo={handelremoveTodo}
        on:toggletodo={handletoggleTodo}
        {loading}
        {error}
        ></PendingTodos>
    
    </div>

    <div class="completed-todos border border-[#E9E9E9] bg-[#FFFFFF] p-6 w-full lg:w-2/4"

        in:slide={{duration:500, delay:300, easing:cubicIn}}
        out:blur={{amount:10, duration:500}}
        on:introstart={()=>console.log('animation start')}
        on:introend={()=>console.log('animation end')}
        on:outrostart={()=>console.log('animation close start')}
        on:outroend={()=>console.log('animation close end')}

    >
        <h2 class="text-xl font-bold text[##3B3333]">Completed todos</h2>

        <CompletedTodos 
        todos={todos && todos.filter((t) => t.completed)}
        on:toggletodo={handletoggleTodo}
        {loading}
        {error}
        ></CompletedTodos>
    </div>

</div>





