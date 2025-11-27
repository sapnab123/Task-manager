<template>
  <div class="p-6 max-w-xl mx-auto">
    <h1 class="text-2xl font-bold mb-4">Task Manager</h1>

    <div class="mb-4 flex">
      <input 
        v-model="newTask" 
        class="border p-2 flex-1" 
        placeholder="Add new task"
      />
      <button @click="createTask" class="bg-blue-600 text-white px-4 ml-2">Add</button>
    </div>

    <ul>
      <li v-for="task in tasks" :key="task.id" class="flex justify-between p-2 border-b">
        <div>
          <input type="checkbox" v-model="task.completed" @change="updateTask(task)" />
          <span :class="{'line-through': task.completed}" class="ml-2">{{ task.title }}</span>
        </div>

        <button @click="deleteTask(task.id)" class="text-red-600">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
import { ref, onMounted } from "vue";
import axios from "axios";

export default {
  setup() {
    const tasks = ref([]);
    const newTask = ref("");

    const getTasks = async () => {
      const res = await axios.get("/api/tasks");
      tasks.value = res.data;
    };

    const createTask = async () => {
      if (!newTask.value) return;
      await axios.post("/api/tasks", { title: newTask.value });
      newTask.value = "";
      getTasks();
    };

    const updateTask = async (task) => {
      await axios.put(`/api/tasks/${task.id}`, task);
    };

    const deleteTask = async (id) => {
      await axios.delete(`/api/tasks/${id}`);
      getTasks();
    };

    onMounted(getTasks);

    return { tasks, newTask, createTask, updateTask, deleteTask };
  }
};
</script>

<style>
.line-through { text-decoration: line-through; }
</style>
