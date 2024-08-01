<script setup>
import { ref, onMounted, computed, watch } from 'vue' // Importing Vue's ref for reactive variables, onMounted for lifecycle hook, computed for computed properties, and watch for watching changes

const todos = ref([]) // Reactive reference for the list of todos
const name = ref('') // Reactive reference for the user's name

const input_content = ref('') // Reactive reference for the input field content
const input_category = ref(null) // Reactive reference for the selected category of the todo

const todos_asc = computed(() => todos.value.sort((a, b) => {
  // Computed property to sort todos by creation date in ascending order
  return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
  // Watcher for the 'name' reference, updates localStorage whenever the name changes
  localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
  // Watcher for the 'todos' reference, updates localStorage whenever the todos list changes
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {
  deep: true // Deep watch to monitor changes within objects/arrays
})

const addTodo = () => {
  // Function to add a new todo item to the list
  if (input_content.value.trim() === '' || input_category.value === null) {
    return // Prevent adding todo if content or category is not provided
  }

  todos.value.push({
    // Adding a new todo item to the todos list with content, category, and other properties
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime()
  })
}

const removeTodo = (todo) => {
  // Function to remove a todo item from the list
  todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
  // Lifecycle hook to initialize data when the component is mounted
  name.value = localStorage.getItem('name') || '' // Load saved name from localStorage or set to an empty string
  todos.value = JSON.parse(localStorage.getItem('todos')) || [] // Load saved todos from localStorage or set to an empty array
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" id="name" placeholder="Name here" v-model="name">
        <!-- Input field for the user's name, bound to the 'name' reactive reference -->
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TO DO LIST</h3>

      <form id="new-todo-form" @submit.prevent="addTodo">
        <!-- Form to create a new todo, prevent default submission behavior and call addTodo method -->
        <h4>What's on your to do list?</h4>
        <input 
          type="text" 
          name="content" 
          id="content" 
          placeholder="e.g. make a video"
          v-model="input_content" />
        <!-- Input field for the todo content, bound to 'input_content' reference -->
        
        <h4>Pick a category</h4>
        <div class="options">

          <label>
            <input 
              type="radio" 
              name="category" 
              id="category1" 
              value="business"
              v-model="input_category" />
            <!-- Radio button for 'Business' category, bound to 'input_category' reference -->
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input 
              type="radio" 
              name="category" 
              id="category2" 
              value="personal"
              v-model="input_category" />
            <!-- Radio button for 'Personal' category, bound to 'input_category' reference -->
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>

        </div>

        <input type="submit" value="Add to do item" />
        <!-- Submit button to add the new todo -->
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">

        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <!-- Loop through the sorted todos list and display each todo item -->
          <label>
            <input type="checkbox" v-model="todo.done" />
            <!-- Checkbox to mark a todo as done, bound to the 'done' property of each todo -->
            <span :class="`bubble ${
              todo.category == 'business' 
                ? 'business' 
                : 'personal'
            }`"></span>
            <!-- Dynamic class for the category bubble based on the todo's category -->
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
            <!-- Input field to edit the todo content, bound to the 'content' property of each todo -->
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
            <!-- Button to delete the todo item, calls removeTodo method with the specific todo -->
          </div>
        </div>

      </div>
    </section>

  </main>
</template>
