<script>
    import { createEventDispatcher } from "svelte";
    import { update_await_block_branch } from "svelte/internal";
    import TodosStatus from "./TodosStatus.svelte";
    export let todo;
    const dispatch = createEventDispatcher();
    let editing = false;
    let name = todo.name;
    function update(updatedTodo) {
        todo = { ...todo, ...updatedTodo };
        dispatch("update", TodosStatus);
    }
    function onCancel() {
        name = todo.name; // restores name to its initial value and
        editing = false; // and exit editing mode
    }

    function onSave() {
        update({ name: name }); // updates todo name
        editing = false; // and exit editing mode
    }

    function onRemove() {
        dispatch("remove", todo); // emit remove event
    }

    function onEdit() {
        editing = true; // enter editing mode
    }

    function onToggle() {
        update({ completed: !todo.completed }); // updates todo status
    }
</script>

<div class="stack-small">
    {#if editing}
        <form
            on:submit|preventDefault={onSave}
            class="stack-small"
            on:keydown={(e) => e.key === "Escape" && onCancel()}
        >
            <div class="form-group">
                <label for="todo-{todo.id}" class="todo-label"
                    >New name for '{todo.name}'</label
                >
                <input
                    bind:value={name}
                    type="text"
                    id="todo-{todo.id}"
                    autoComplete="off"
                    class="todo-text"
                />
            </div>
            <div class="btn-group">
                <button
                    class="btn todo-cancel"
                    on:click={onCancel}
                    type="button"
                >
                    Cancel<span class="visually-hidden"
                        >renaming {todo.name}</span
                    >
                </button>
                <button
                    class="btn btn__primary todo-edit"
                    type="submit"
                    disabled={!name}
                >
                    Save<span class="visually-hidden"
                        >new name for {todo.name}</span
                    >
                </button>
            </div>
        </form>
    {:else}
        <div class="c-cb">
            <input
                type="checkbox"
                id="todo-{todo.id}"
                on:click={onToggle}
                checked={todo.completed}
            />
            <label for="todo-{todo.id}" class="todo-label">{todo.name}</label>
        </div>
        <div class="btn-group">
            <button type="button" class="btn">
                Edit <span class="visually-hidden">{todo.name}</span>
            </button>
            <button
                type="button"
                class="btn btn__danger"
                on:click={() => dispatch("remove", todo)}
            >
                Delete <span class="visually-hidden">{todo.name}</span>
            </button>
        </div>
    {/if}
</div>
