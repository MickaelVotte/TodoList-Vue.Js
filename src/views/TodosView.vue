<script setup>
import { ref, watch, computed } from 'vue';
import { uid } from "uid";
import { Icon } from '@iconify/vue';
import TodoCreator from '../components/TodoCreator.vue';
import TodoItem from '../components/TodoItem.vue';

const todoList = ref([]);

watch(todoList, () => {
  setTodoListLocalStorage()
},
  {
    deep: true,
  });

//It returns true if all elements in the todoList have the isCompleted property equal to true, otherwise it returns false 
//The every method is used to check if all elements in the list meet this condition
const todoCompleted = computed(() => {
  return todoList.value.every((todo) => todo.isCompleted);
})


const fetchTodoList = () => {
  const savedTodoList = JSON.parse(localStorage.getItem('todoList'))
  if (savedTodoList) {
    todoList.value = savedTodoList
  }
};

fetchTodoList();

const setTodoListLocalStorage = () => {
  localStorage.setItem("todoList", JSON.stringify(todoList.value))

};


// function to push a new task in "todolist" array 
const createTodo = (todo) => {
  todoList.value.push({
    //uid() generate a unique ID
    id: uid(),
    todo,
    isCompleted: null,
    isEditing: null,
  });
  setTodoListLocalStorage()
};

//This function modifies the isCompleted proprety of the element in the todoList at index todoPros by inverting its current value
const toggleTodoComplete = (todoPros) => {
  todoList.value[todoPros].isCompleted = !todoList.value[todoPros].isCompleted;
}

const toggleEditTodo = (todoPros) => {
  todoList.value[todoPros].isEditing = !todoList.value[todoPros].isEditing;
}

//This function updates the todo proprety of the element in the todoList at index todoPros by assigning it the value of todoVal
const updateTodo = (todoVal, todoPos) => {
  todoList.value[todoPos].todo = todoVal
}

//This function updates the todoList by removing the element whose id property is equal to todoId
const deleteTodo = (todoId) => {
  todoList.value = todoList.value.filter((todo) => todo.id !== todoId)
};
</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <!-- listen custom emitted event "create-todo"-->
    <!-- the createTodo function is triggered when the event is trigger -->
    <TodoCreator @create-todo="createTodo" />
    <ul class="todo-list" v-if="todoList.length > 0">
      <!-- :todo, index prop -->
      <TodoItem v-for="(todo, index) in todoList" :todo="todo" :index="index" @toggle-complete="toggleTodoComplete"
        @edit-todo="toggleEditTodo" @update-todo="updateTodo" @delete-todo="deleteTodo" />
    </ul>
    <p class="todos-msg" v-else>
      <Icon icon="ph:smiley-sad" width="22" />
      <span>You have no todo's to complete! Add one!</span>
    </p>
    <p v-if="todoCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper" />
      <span>You have completed all your todos!</span>
    </p>
  </main>
</template>


<style lang="scss" scoped>
main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }
}

.todo-list {
  display: flex;
  flex-direction: column;
  list-style: none;
  margin-top: 24px;
  gap: 20px;
}

.todos-msg {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 8px;
  margin-top: 24px;
}
</style>
