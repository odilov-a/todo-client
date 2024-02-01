<template>
  <div class="container mx-auto p-6">
    <h1 class="text-3xl font-bold mb-6">Todo API</h1>
    <form @submit.prevent="addOrUpdateTodo" class="mt-4">
      <label class="block mb-2">Task:</label>
      <input v-model="newTodo.task" required class="border border-slate-600 px-3 py-2 mb-2" />
      <label class="block mb-2">Description:</label>
      <textarea v-model="newTodo.description" required class="border border-slate-600 px-3 py-2 mb-2" />
      <label class="block mb-2">Completed:</label>
      <input type="checkbox" v-model="newTodo.completed" class="mr-2" />
      <button type="submit" class="bg-green-500 text-white px-4 py-2 rounded mt-4">
        {{ editingTodo ? "Update" : "Add" }} Todo
      </button>
    </form>
    <br/>
    <table class="min-w-full border border-slate-500">
      <thead>
        <tr>
          <th class="py-2 px-4 border border-slate-600">Status</th>
          <th class="py-2 px-4 border border-slate-600">Task</th>
          <th class="py-2 px-4 border border-slate-600">Description</th>
          <th class="py-2 px-4 border border-slate-600">Action</th>
          <th class="py-2 px-4 border border-slate-600">Action</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="item in apiData" :key="item._id">
          <td class="py-2 px-4 border border-slate-700">
            <input type="checkbox" :checked="item.completed" @change="updateStatus(item)" :disabled="item.completed" class="form-checkbox text-blue-500"/>
          </td>
          <td class="py-2 px-4 border border-slate-700">{{ item.task }}</td>
          <td class="py-2 px-4 border border-slate-700">{{ item.description }}</td>
          <td class="py-2 px-4 border border-slate-700">
            <button @click="editTodo(item)" class="bg-blue-500 text-white px-4 py-1 rounded">Edit</button>
          </td>
          <td class="py-2 px-4 border border-slate-700">
            <button @click="deleteTodo(item._id)" class="bg-red-500 text-white px-4 py-1 rounded">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
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
      await axios.put(
        `http://localhost:8080/api/todos/${editingTodo.value._id}`,
        newTodo.value
      );
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

const updateStatus = async (task) => {
  try {
    await axios.put(`http://localhost:8080/api/todos/${task._id}`, { completed: !task.completed });
    fetchTodos();
  } catch (error) {
    console.error("Error updating status:", error);
  }
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
