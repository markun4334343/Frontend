<template>
  <div id="app">
    <!-- This is the header -->
    <header>
      <h1>My Vue To-Do List</h1>
      <p>A simple app powered by Java (soon!)</p>
    </header>

    <!-- This is the input form to add new items -->
    <div class="input-section">
      <input
          v-model="newTask"
          @keyup.enter="addTask"
          placeholder="What needs to be done?"
          class="task-input"
      >
      <button @click="addTask" class="add-btn">Add Task</button>
    </div>

    <!-- This is the list of tasks -->
    <div class="task-list">
      <!-- We loop through the tasks array and create a div for each one -->
      <div v-for="(task, index) in tasks" :key="index" class="task-item">

        <!-- Checkbox to mark task as complete -->
        <input type="checkbox" v-model="task.completed" class="checkbox">

        <!-- The task text. Style changes if it's completed -->
        <span :class="{ 'completed': task.completed }" class="task-text">
          {{ task.text }}
        </span>

        <!-- Button to delete the task -->
        <button @click="removeTask(index)" class="delete-btn">×</button>
      </div>

      <!-- This message shows if the list is empty -->
      <p v-if="tasks.length === 0" class="empty-message">
        Your to-do list is empty. Add a task above!
      </p>
    </div>

    <!-- Simple footer -->
    <footer>
      <p>Total Tasks: {{ tasks.length }} | Completed: {{ completedCount }}</p>
    </footer>
  </div>
</template>

<script>
// Import the CSS file
import '@/assets/style.css';
// Import the API base URL
import { API_BASE } from '@/config/api';

export default {
  name: 'App',
  data() {
    return {
      newTask: '',
      tasks: []    // This will now come from backend
    }
  },
  computed: {
    completedCount() {
      return this.tasks.filter(task => task.completed).length;
    }
  },
  // Load tasks from backend when component mounts
  async mounted() {
    await this.loadTasks();
  },
  methods: {
    // Load tasks from backend
    async loadTasks() {
      try {
        const response = await fetch(`${API_BASE}/api/tasks`);
        this.tasks = await response.json();
      } catch (error) {
        console.error('Failed to load tasks:', error);
        // Fallback to local storage
        this.tasks = JSON.parse(localStorage.getItem('tasks') || '[]');
      }
    },

    // Add task to backend
    async addTask() {
      if (this.newTask.trim() !== '') {
        try {
          const response = await fetch(`${API_BASE}/api/tasks`, {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify({
              text: this.newTask,
              completed: false
            })
          });

          const newTask = await response.json();
          this.tasks.push(newTask);
          this.newTask = '';
        } catch (error) {
          console.error('Failed to add task:', error);
          // Fallback: add locally
          this.tasks.push({
            text: this.newTask,
            completed: false
          });
          this.newTask = '';
          localStorage.setItem('tasks', JSON.stringify(this.tasks));
        }
      }
    },

    // Remove task from backend
    async removeTask(index) {
      const task = this.tasks[index];
      try {
        // If task has an ID from backend, use it. Otherwise use index as fallback
        if (task.id) {
          await fetch(`${API_BASE}/api/tasks/${task.id}`, {
            method: 'DELETE'
          });
        }
        this.tasks.splice(index, 1);
      } catch (error) {
        console.error('Failed to delete task:', error);
        // Fallback: remove locally
        this.tasks.splice(index, 1);
        localStorage.setItem('tasks', JSON.stringify(this.tasks));
      }
    }
  }
}
</script>