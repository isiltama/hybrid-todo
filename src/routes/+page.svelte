<script>
    import { onMount } from 'svelte';
    import { writable } from 'svelte/store';

    let todoItem = '';
    let category = 'Personal'; // Declare category variable with default value
    let storedList;
    let todoList = writable([]);

    // Load saved list from local storage on component mount
    onMount(() => {
        storedList = localStorage.getItem('storedList');
        if(storedList) {
            todoList.set(JSON.parse(storedList));
        }
    });

    // Function to update local storage with current list
    function updateList(list) {
        localStorage.setItem('storedList', JSON.stringify(list));
    }

    $: isDone = $todoList.filter(item => item.done);

    // Add category to task
    function addToArray() {
        if (todoItem.trim() === '') {
            return;
        }

        todoList.update(list => {
            const updatedList = [...list, {
                text: todoItem,
                category: category,
                done: false
            }];
            updateList(updatedList);
            return updatedList;
        });

        todoItem = '';
    }

    function removeThis(index) {
        todoList.update(list => {
            list.splice(index, 1);
            updateList(list);
            return list;
        });
    }

    function clearDone() {
        todoList.update(list => {
            const filteredList = list.filter(item => !item.done);
            updateList(filteredList);
            return filteredList;
        });
    }

    function moveItemUp(index) {
        if (index > 0) {
            todoList.update(list => {
                const tempList = [...list];
                const temp = tempList[index];
                tempList[index] = tempList[index - 1];
                tempList[index - 1] = temp;
                updateList(tempList);
                return tempList;
            });
        }
    }

    function moveItemDown(index) {
        if (index < $todoList.length - 1) {
            todoList.update(list => {
                const tempList = [...list];
                const temp = tempList[index];
                tempList[index] = tempList[index + 1];
                tempList[index + 1] = temp;
                updateList(tempList);
                return tempList;
            });
        }
    }

    function editItem(index, newText) {
        todoList.update(list => {
            list[index].text = newText;
            updateList(list);
            return list;
        });
    }
</script>

<style>
    html, body {
        margin: 0;
        padding: 0;
        background-color: #D2B48C; /* Vintage rustic background */
        font-family: Arial, sans-serif;
        color: #fff;
        height: 100%; /* Ensure the body takes up the entire viewport height */
    }

    .container {
        max-width: 600px;
        margin: 0 auto;
        padding: 20px;
        background-color: #F5DEB3; /* Light tan background color */
        border-radius: 10px; /* Optional: Add border radius for better appearance */
    }

    h1 {
        font-size: 2.5rem;
        margin-bottom: 20px;
        text-align: center;
    }

    form {
        display: flex;
        margin-bottom: 20px;
    }

    input[type="text"] {
        flex: 1;
        padding: 10px;
        border: none;
        border-radius: 5px 0 0 5px;
        font-size: 16px;
    }

    select {
        padding: 10px;
        border: none;
        border-radius: 0 5px 5px 0;
        background-color: #fff;
        font-size: 16px;
    }

    button {
        padding: 10px 20px;
        border: none;
        background-color: #007bff;
        color: #fff;
        border-radius: 5px;
        cursor: pointer;
        font-size: 16px;
        transition: all 0.3s ease;
    }

    button:hover {
        background-color: #0056b3;
    }

    ul {
        list-style: none;
        padding: 0;
    }

    .todo-list li {
        display: flex;
        align-items: center;
        margin-bottom: 10px;
    }

    .todo-list .done {
        text-decoration: line-through;
        color: #999;
    }

    li {
        font-size: 1.5rem;
        padding: 10px;
    }

    .remove {
        margin-left: auto;
        color: darkred;
        cursor: pointer;
    }

    .edit {
        margin-left: 10px;
        color: #007bff;
        cursor: pointer;
    }

    .category {
        margin-left: 10px;
        color: #999;
        font-size: 14px;
    }

    .up-down {
        margin-left: 10px;
        color: #007bff;
        cursor: pointer;
    }
</style>


<div class="container">
    <h1>Sh💩t To Do</h1>

    <form on:submit|preventDefault={addToArray}>
        <input type="text" placeholder="Add a new task" bind:value={todoItem}>
        <select bind:value={category}>
            <option value="Personal">Personal</option>
            <option value="Work">Work</option>
            <option value="Home">Home</option>
        </select>
        <button type="submit">Add</button>
    </form>

    <ul class="todo-list">
        {#each $todoList as item, index}
        <li>
            <input type="checkbox" bind:checked={item.done} on:change={() => updateList($todoList)}>
            <span class:done={item.done}>{item.text}</span>
            <span class="category">{item.category}</span>
            <span class="up-down" on:click={() => moveItemUp(index)}>&#9650;</span>
            <span class="up-down" on:click={() => moveItemDown(index)}>&#9660;</span>
            <span on:click={() => removeThis(index)} class="remove" role="button" tabindex="0">🗑️</span>
            <span on:click={() => editItem(index, prompt('Edit task:', item.text))} class="edit" role="button" tabindex="0">✏️</span>
        </li>
        {/each}
    </ul>

    {#if isDone.length > 0}
    <button on:click={clearDone}>Remove Completed</button>
    {/if}
</div>


