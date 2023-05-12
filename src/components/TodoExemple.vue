<script setup>
import { ref } from 'vue'
import TodoItem from './todoItem.vue'
import { computed } from '@vue/reactivity';
  
const newTodoText = ref('')
const todos = ref([
  {
    id: 0,
    title: 'Do the dishes'
  },
  {
    id: 1,
    title: 'Take out the trash'
  },
  {
    id: 2,
    title: 'Mow the lawn'
  }
])

let nextTodoId = todos.value.length

function addNewTodo() {
  todos.value.push({
    id: nextTodoId++,
    title: newTodoText.value
  })
  newTodoText.value = ''
}
</script>

<template>
    <form v-on:submit.prevent="addNewTodo">
        <label for="new-todo">Add a todo</label>
        <input
            v-model="newTodoText"
            id="new-todo"
            placeholder="E.g. Feed the cat"
        />
        <button>Add</button>
  </form>
  <ul>
    <todo-item
      v-for="(todo, index) in todos"
      :key="todo.id"
      :title="todo.title"
      @remove="todos.splice(index, 1)"
    ></todo-item>
  </ul>
</template>