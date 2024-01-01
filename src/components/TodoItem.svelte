<script>
    // @ts-nocheck

    export let index
    export let todo
    export let editTodo
    export let removeTodo
</script>

<li class="todo">
    <p>{todo}</p>

    <div class="actions">
        <!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
        <p on:click={() => editTodo(index)} on:keydown={() => {}}>Edit</p>

        <!-- svelte-ignore a11y-no-noninteractive-element-interactions -->
        <p on:click={() => removeTodo(index)} on:keydown={() => {}}>Delete</p>
    </div>
</li>

<style>
    .todo {
        border-left: 1px solid cyan;
        padding: 4px 8px;
        display: flex;
        align-items: center;
        justify-content: space-between;
    }

    .actions {
        display: flex;
        align-items: center;
        gap: 12px;
    }

    .actions p {
        cursor: pointer;
    }

    .actions p:hover {
        color: coral;
    }
</style>