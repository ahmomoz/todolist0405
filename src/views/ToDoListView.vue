<template>
  <div id="todoListPage" class="bg-half">
    <div class="container todoListPage vhContainer">
      <div class="todoList_Content">
        <div class="inputBox">
          <input type="text" placeholder="請輸入待辦事項" v-model="text" @keyup.enter="addTodo">
          <a href="#" @click.prevent="addTodo">
            <i class="fa fa-plus"></i>
          </a>
        </div>
        <div class="todoList_list">
          <ul class="todoList_tab">
            <li>
              <a href="#" :class="{active: visibility===''}"
                @click.prevent="visibility=''">
                全部</a>
              </li>
            <li>
              <a href="#" :class="{active: visibility==='active'}"
                @click.prevent="visibility='active'">
                待完成</a>
            </li>
            <li>
              <a href="#" :class="{active: visibility==='complete'}"
                @click.prevent="visibility='complete'">
                已完成</a>
            </li>
          </ul>
          <div class="todoList_items">
            <ul class="todoList_item">
              <li v-for="todo in filterTodos" :key="todo.id">
                <label v-if="editTodoId === todo.id">
                  <input type="text" placeholder="請輸入待辦事項" v-model="editTodoText" @keyup.enter="completeEdit(todo)">
                </label>
                <label class="todoList_label" v-else>
                  <input class="todoList_input" type="checkbox" v-model="todo.done" value="true">
                  <span @dblclick="editTodo(todo)">{{ todo.text }}</span>
                </label>
                <a href="#" @click.prevent="removeTodo(todo.id)">
                  <i class="fa fa-times"></i>
                </a>
              </li>
            </ul>
            <div class="todoList_statistics">
              <p> {{ todos.filter((todo) => todo.done).length }}  個已完成項目</p>
              <a href="#" @click.prevent="removeAllTodo">清除已完成項目</a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, ref, watchEffect } from 'vue'

const localData = localStorage.getItem('todo-data') || '[]'

const text = ref(null)
const todos = ref(JSON.parse(localData))

const addTodo = () => {
  if (text.value) {
    todos.value.push({
      id: Date.now(),
      text: text.value,
      done: false
    })
  }
  text.value = ''
}

const editTodoId = ref(null)
const editTodoText = ref(null)
const editTodo = (todo) => {
  editTodoId.value = todo.id
  editTodoText.value = todo.text
}
const completeEdit = (todo) => {
  const index = todos.value.findIndex((t) => t.id === todo.id)
  todos.value[index].text = editTodoText.value
  editTodoId.value = ''
  editTodoText.value = ''
}

const removeTodo = (todoId) => {
  todos.value = todos.value.filter(todo => todo.id !== todoId)
}
const removeAllTodo = () => {
  todos.value = todos.value.filter(todo => !todo.done)
}

watchEffect(() => {
  localStorage.setItem('todo-data', JSON.stringify(todos.value))
})

const visibility = ref(null)
const filterTodos = computed(() => {
  if (visibility.value === 'active') { return todos.value.filter(todo => !todo.done) }
  if (visibility.value === 'complete') { return todos.value.filter(todo => todo.done) }
  return todos.value
})

</script>

<style scoped>
</style>
