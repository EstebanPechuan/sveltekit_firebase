<script>
    // @ts-nocheck
    import TodoItem from "../../components/TodoItem.svelte";
    import { db } from "$lib/firebase/firebase";
    import { authHandlers, authStore } from "../../store/store";
    import { doc, getDoc, setDoc } from "firebase/firestore";

    let todoList = [];
    let currTodo = '';
    let error = false;
    let editTodoButton = false;
    let indexAux = null

    authStore.subscribe((curr) => {
        todoList = curr.data.todos
    })

    function addTodo() {
        error = false;
        if (!currTodo) {
            error = true;
        } else {
            todoList = [...todoList, currTodo];
            currTodo = '';

            document.querySelector('#addTodo').focus()
        }
    }

    function handleEditTodo(index) {
        editTodoButton = true
        currTodo = todoList[index]
        
        indexAux = index

        document.querySelector('#addTodo').focus()
    }

    function editTodo() {
        todoList[indexAux] = currTodo
        indexAux = null
        editTodoButton = false
        
        document.querySelector('#addTodo').focus()
        currTodo = '';
    }

    function removeTodo(index) {
        let newTodoList = todoList.filter((item, i) => {
            return i !== index
        })

        todoList = newTodoList
    }

    async function saveTodos() {
        try {
            const userRef = doc(db, 'users', $authStore.user.uid)
            await setDoc(
                userRef,
                {
                    todos: todoList
                },
                { merge: true }
            )
        } catch (error) {
            console.log('There was an error saving your information');
        }
    }
</script>

{#if !$authStore.loading}
    <div class="mainContainer"> 
        <div class="headerContainer">
            <h1>Todo List</h1>
            
            <div class="btns">
                <button on:click={saveTodos}>Save</button>
                <button on:click={authHandlers.logout}>Logout</button>
            </div>
        </div>

        <main>
            {#if todoList.length === 0}
                <p>You have nothing to do...</p>
            {/if}
            <ol>
                {#each todoList as todo, index}
                    <TodoItem
                        todo={todo}
                        index={index}
                        removeTodo={removeTodo}
                        editTodo={handleEditTodo}
                    />
                {/each}
            </ol>
        </main>
        
        <div class={"enterTodo " + (error ? "errorBorder": '')}>
            <input id="addTodo" bind:value={currTodo} type="text" placeholder="Enter todo">

            {#if !editTodoButton}
                <button on:click={addTodo}>ADD</button>
            {:else}
                <button on:click={editTodo}>EDIT</button>
            {/if}
        </div>
    </div>

{/if}

<style>
    .mainContainer {
        display: flex;
        flex-direction: column;
        min-height: 100vh;
        gap: 24px;
        padding: 24px;
        width: 100%;
        max-width: 1000px;
        margin: 0 auto;
    }

    .headerContainer {
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    .headerContainer button {
        background: #003c5b;
        color: white;
        padding: 10px 18px;
        border: none;
        border-radius: 4px;
        font-weight: 700;
        display: flex;
        align-items: center;
        gap: 10px;
        cursor: pointer;
    }

    .headerContainer button:hover {
        opacity: 0.7;
    }

    .btns {
        display: flex;
        justify-content: center;
        gap: 14px;
    }

    main {
        display: flex;
        flex-direction: column;
        gap: 8px;
        flex: 1;
    }

    main ol {
        display: flex;
        flex-direction: column;
        gap: 8px;
    }

    .enterTodo {
        display: flex;
        align-items: stretch;
        border: 1px solid #0891b2;
        border-radius: 5px;
        overflow: hidden;
    }

    .errorBorder {
        border-color: coral !important;
    }

    .enterTodo input {
        background: transparent;
        border: none;
        outline: none;
        padding: 24px;
        color: white;
        flex: 1;
    }

    .enterTodo button {
        padding: 0 28px;
        background: #003c5b;
        border: none;
        color: cyan;
        font-weight: 600;
        cursor: pointer;
    }

    .enterTodo button:hover {
        background: transparent;
    }
</style>