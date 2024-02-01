<template>
  <div>
    <h1>Todo API</h1>
    <ul>
      <li v-for="item in apiData" :key="item._id">
        {{ item.task }} - {{ item.description }} - {{ item.completed }}
        <button @click="editTodo(item)">Edit</button>
        <button @click="deleteTodo(item._id)">Delete</button>
      </li>
    </ul>

    <form @submit.prevent="addOrUpdateTodo">
      <label>Task: </label>
      <input v-model="newTodo.task" required />
      <label>Description: </label>
      <input v-model="newTodo.description" required />
      <label>Completed: </label>
      <input type="checkbox" v-model="newTodo.completed" />
      <button type="submit">{{ editingTodo ? 'Update' : 'Add' }} Todo</button>
    </form>
  </div>
</template>

<script setup>
import axios from "axios";

const apiData = ref([]);
const newTodo = ref({ task: "", description: "", completed: false });
const editingTodo = ref(null);

const fetchTodos = async () => {
  try {
    const response = await axios.get("http://localhost:8080/api/todos");
    apiData.value = response.data;
  } catch (error) {
    console.error("Error fetching data:", error);
  }
};

const addOrUpdateTodo = async () => {
  try {
    if (editingTodo.value) {
      await axios.put(`http://localhost:8080/api/todos/${editingTodo.value._id}`, newTodo.value);
    } else {
      await axios.post("http://localhost:8080/api/todos", newTodo.value);
    }
    newTodo.value = { task: "", description: "", completed: false };
    editingTodo.value = null;
    fetchTodos();
  } catch (error) {
    console.error("Error adding/updating todo:", error);
  }
};

const editTodo = (todo) => {
  editingTodo.value = todo;
  newTodo.value = { ...todo };
};

const deleteTodo = async (todoId) => {
  try {
    await axios.delete(`http://localhost:8080/api/todos/${todoId}`);
    fetchTodos();
  } catch (error) {
    console.error("Error deleting todo:", error);
  }
};

onMounted(fetchTodos);
</script>
