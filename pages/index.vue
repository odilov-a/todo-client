<template>
  <div>
    <nav class="bg-gray-800 p-4 text-white">
      <div class="container mx-auto flex justify-between items-center">
        <h1 class="text-4xl font-extrabold tracking-wider">Todo App</h1>
        <div class="space-x-4">
          <NuxtLink href="/graphs" class="text-xl font-semibold">Graphs Page</NuxtLink>
          <NuxtLink href="/docs" class="text-xl font-semibold">Docs Page</NuxtLink>
        </div>
      </div>
    </nav>

    <div class="container mx-auto p-6">
      <div v-if="loading" class="text-center mt-8">
        <div class="loader"></div>
        <p class="text-gray-600">Loading...</p>
      </div>

      <form v-else @submit.prevent="addOrUpdateTodo" class="mt-4">
        <label class="block mb-2 font-bold">Task:</label>
        <input v-model="newTodo.task" required placeholder="Task name"
          class="border border-slate-600 px-3 py-2 mb-2 w-full" />
        <label class="block mb-2 font-bold">Description:</label>
        <textarea v-model="newTodo.description" required placeholder="Task description"
          class="border border-slate-600 px-3 py-2 mb-2 w-full h-20" />
        <div class="flex items-center mr-2">
          <label class="mr-2 font-bold">Completed:</label>
          <input type="checkbox" v-model="newTodo.completed" class="mr-2" />
          <button type="submit" class="bg-green-500 text-white px-4 py-2 rounded mt-4">
            {{ editingTodo ? "Update" : "Add" }} Todo
          </button>
        </div>
      </form>
      <br />
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
              <input type="checkbox" :checked="item.completed" @change="updateStatus(item)" :disabled="item.completed"
                class="form-checkbox text-blue-500" />
            </td>
            <td class="py-2 px-4 border border-slate-700">{{ item.task }}</td>
            <td class="py-2 px-4 border border-slate-700">
              {{ item.description }}
            </td>
            <td class="py-2 px-4 border border-slate-700">
              <button @click="editTodo(item)" class="bg-blue-500 text-white px-4 py-1 rounded">
                Edit
              </button>
            </td>
            <td class="py-2 px-4 border border-slate-700">
              <button @click="deleteTodo(item._id)" class="bg-red-500 text-white px-4 py-1 rounded">
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<style>
.loader {
  width: 50px;
  aspect-ratio: 1;
  display: grid;
  border: 4px solid #0000;
  border-radius: 50%;
  border-right-color: #25b09b;
  animation: l15 1s infinite linear;
}

.loader::before,
.loader::after {
  content: "";
  grid-area: 1/1;
  margin: 2px;
  border: inherit;
  border-radius: 50%;
  animation: l15 2s infinite;
}

.loader::after {
  margin: 8px;
  animation-duration: 3s;
}

@keyframes l15 {
  100% {
    transform: rotate(1turn);
  }
}
</style>

<script setup>
import axios from "axios";
import Swal from "sweetalert2/dist/sweetalert2.all.min.js";

const apiData = ref([]);
const newTodo = ref({ task: "", description: "", completed: false });
const editingTodo = ref(null);
const loading = ref(false);

const fetchTodos = async () => {
  try {
    loading.value = true;
    const response = await axios.get("http://localhost:8080/api/todos");
    apiData.value = response.data;
  } catch (error) {
    console.error("Error fetching data:", error);
  } finally {
    loading.value = false;
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
    await axios.put(`http://localhost:8080/api/todos/${task._id}`, {
      completed: !task.completed,
    });
    fetchTodos();
  } catch (error) {
    console.error("Error updating status:", error);
  }
};

const deleteTodo = async (todoId) => {
  try {
    const result = await Swal.fire({
      title: "Are you sure?",
      text: "You won't be able to revert this!",
      icon: "warning",
      showCancelButton: true,
      confirmButtonColor: "#3085d6",
      cancelButtonColor: "#d33",
      confirmButtonText: "Yes, delete it!",
    });
    if (result.isConfirmed) {
      await axios.delete(`http://localhost:8080/api/todos/${todoId}`);
      fetchTodos();
      Swal.fire("Deleted!", "Your task has been deleted.", "success");
    }
  } catch (error) {
    console.error("Error deleting todo:", error);
    Swal.fire("Error", "There was an error deleting the task.", "error");
  }
};

onMounted(fetchTodos);
</script>