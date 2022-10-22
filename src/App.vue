<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

// created at untuk sort array object / asc tu ascending mana yg buat dulu dia akan kat atas 
const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  // kalau salah satu condition content value tkde atau category null jgn buat pape 
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime()
  })
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}
//json data exchange to string, deep true untuk check changes setiap string 
watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

// nak tgok data kalau dia change new value dia akan print new value jugak 
watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

// dia akan save ke local storage bila refresh page dia masih kat situ jugak 
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  // pass in array 
  todos.value = JSON.parse(localStorage.getItem('todos')) || []

})
</script>

<template>

  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>
    <section class="create-todo">
      <h3>Create a todo</h3>
      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>What's on your to do list?</h4>
        <input type="text" placeholder="Solat tahajud" v-model="input_content" />
        <h4>Choose a category</h4>
        <div class="option">
          <label>
            <input type="radio" name="category" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>business</div>
          </label>

          <label>
            <input type="radio" name="category" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />

      </form>
    </section>
    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${ todo.category == 'business' ? 'business' : 'personal'}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>

  </main>
</template>


