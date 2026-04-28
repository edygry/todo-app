<template>
  <div class="app">
    <h1>Todo</h1>
    
    <div class="input-row">
      <input
        v-model="newTodo"
        @keyup.enter="addTodo"
        placeholder="What needs to be done?"
        autofocus
      />
      <button @click="addTodo">Add</button>
    </div>

    <ul v-if="todos.length" class="todo-list">
      <li v-for="todo in todos" :key="todo.id" :class="{ done: todo.done }">
        <label>
          <input type="checkbox" v-model="todo.done" />
          <span>{{ todo.text }}</span>
        </label>
        <button class="delete" @click="removeTodo(todo.id)">×</button>
      </li>
    </ul>

    <p v-else class="empty">Nothing to do.</p>

    <div class="footer" v-if="todos.length">
      <span>{{ doneCount }} / {{ todos.length }} done</span>
      <button v-if="doneCount" class="clear" @click="clearDone">Clear done</button>
    </div>
  </div>
</template>

<script>
import { ref, computed, watch } from 'vue'

const STORAGE_KEY = 'todos'

function loadTodos() {
  try {
    const data = localStorage.getItem(STORAGE_KEY)
    return data ? JSON.parse(data) : []
  } catch {
    return []
  }
}

export default {
  name: 'App',
  setup() {
    const todos = ref(loadTodos())
    const newTodo = ref('')

    const doneCount = computed(() => todos.value.filter(t => t.done).length)

    watch(todos, (val) => {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(val))
    }, { deep: true })

    function addTodo() {
      const text = newTodo.value.trim()
      if (!text) return
      todos.value.unshift({
        id: Date.now(),
        text,
        done: false
      })
      newTodo.value = ''
    }

    function removeTodo(id) {
      todos.value = todos.value.filter(t => t.id !== id)
    }

    function clearDone() {
      todos.value = todos.value.filter(t => !t.done)
    }

    return {
      todos,
      newTodo,
      doneCount,
      addTodo,
      removeTodo,
      clearDone
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  background: #f5f5f5;
  color: #333;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  padding: 40px 16px;
}

.app {
  width: 100%;
  max-width: 480px;
}

h1 {
  font-size: 28px;
  font-weight: 600;
  margin-bottom: 24px;
  color: #111;
}

.input-row {
  display: flex;
  gap: 8px;
  margin-bottom: 24px;
}

.input-row input {
  flex: 1;
  padding: 12px 16px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 16px;
  outline: none;
  transition: border-color 0.2s;
}

.input-row input:focus {
  border-color: #4a90d9;
}

.input-row button {
  padding: 12px 20px;
  background: #4a90d9;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 16px;
  cursor: pointer;
  transition: background 0.2s;
}

.input-row button:hover {
  background: #357abd;
}

.todo-list {
  list-style: none;
}

.todo-list li {
  display: flex;
  align-items: center;
  padding: 12px 16px;
  background: white;
  border-radius: 8px;
  margin-bottom: 8px;
  transition: opacity 0.2s;
}

.todo-list li label {
  flex: 1;
  display: flex;
  align-items: center;
  gap: 12px;
  cursor: pointer;
  font-size: 16px;
}

.todo-list li input[type="checkbox"] {
  width: 20px;
  height: 20px;
  cursor: pointer;
  accent-color: #4a90d9;
}

.todo-list li.done span {
  text-decoration: line-through;
  color: #999;
}

.todo-list li .delete {
  background: none;
  border: none;
  font-size: 20px;
  color: #ccc;
  cursor: pointer;
  padding: 0 4px;
  transition: color 0.2s;
}

.todo-list li .delete:hover {
  color: #e74c3c;
}

.empty {
  text-align: center;
  color: #999;
  padding: 40px 0;
  font-size: 16px;
}

.footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 16px;
  padding: 12px 16px;
  color: #999;
  font-size: 14px;
}

.footer .clear {
  background: none;
  border: none;
  color: #4a90d9;
  cursor: pointer;
  font-size: 14px;
  text-decoration: underline;
}

.footer .clear:hover {
  color: #357abd;
}
</style>
