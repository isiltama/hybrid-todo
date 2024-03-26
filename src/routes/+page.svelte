<script>
    import '../style.css';

    let todoItem = '';
    let urgent, someday;
    let todoList = [];

    $: isDone = todoList.filter(item => item.done);
    $: somedayList = todoList.filter(item => item.someday);

    function addToArray() {
        if (todoItem.trim() === '') {
            return;
        }

        todoList = [...todoList, {
            text: todoItem,
            done: false,
            urgent: urgent,
            someday: someday
        }];

        todoItem = '';
    }

    function removeThis(index) {
        todoList.splice(index, 1);
        todoList = [...todoList];
    }

    function clearDone() {
        todoList = todoList.filter(item => !item.done);
    }
</script>

<div class="container">
    <h1>Your To Do List</h1>

    <form on:submit|preventDefault={addToArray}>
        <input type="text" placeholder="Add a new todo" bind:value={todoItem}>
        <button type="submit">Add</button>
    </form>

    <ul class="todo-list">
        {#each todoList as item, index}
        <li class="{item.urgent ? 'urgent' : ''}">
            <input type="checkbox" bind:checked={item.done}>
            <span class:done={item.done}>{item.text}</span>
            <span on:click={() => removeThis(index)} class="remove" role="button" tabindex="0">🗑️</span> <!-- Using Unicode character for trash can emoji -->
        </li>
        {/each}
    </ul>

    {#if somedayList.length > 0}
    <h2>Someday</h2>
    <ul class="someday-list">
        {#each somedayList as item, index}
        <li>{item.text}</li>
        {/each}
    </ul>
    {/if}

    {#if isDone.length > 0}
    <button on:click={clearDone} class="clear-done">Remove Done</button>
    {/if}
</div>

<style>
    .container {
        max-width: 600px;
        margin: 0 auto;
        padding: 20px;
        color: #333;
    }

    h1 {
        font-size: 2.5rem;
        margin-bottom: 20px;
        text-align: center;
        color: #333;
        text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
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

    button {
        padding: 15px 30px;
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
        font-size: 1.5rem; /* Increase font size to make items more prominent */
        padding: 10px; /* Add padding for better spacing */
    }


    .remove {
        margin-left: auto;
        color: darkred;
        cursor: pointer;
        font-size: 24px; /* Increase font size */
        background-color: rgba(255, 255, 255, 0.6); /* Increase opacity by changing the alpha value */
        padding: 2px; /* Add padding for better visibility */
        width: 26px; /* Set width to make the shape fit the emoji */
        height: 26px; /* Set height to make the shape fit the emoji */
        display: inline-flex; /* Use flexbox for alignment */
        align-items: center; /* Center the emoji vertically */
        justify-content: center; /* Center the emoji horizontally */
        border-radius: 3px; /* Add a slight border radius for the box shape */
        box-shadow: 0 1px 2px rgba(0, 0, 0, 0.5); /* Add a smaller, more opaque shadow effect */
    }


    .clear-done {
        background-color: darkred;
        color: #fff;
        border: none;
        padding: 15px 30px;
        border-radius: 5px;
        cursor: pointer;
        margin-top: 20px;
        transition: all 0.3s ease;
    }

    .clear-done:hover {
        background-color: #ad0000;
    }

    input[type="checkbox"] {
        width: 20px; /* Increase width to make the checkbox bigger */
        height: 20px; /* Increase height to make the checkbox bigger */
        border-radius: 50%; /* Make the checkbox circular */
    }
</style>
