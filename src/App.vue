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

export default {
  name: 'App',
  // Data function returns the reactive state for our component
  data() {
    return {
      newTask: '', // Holds the text from the input field
      tasks: []    // This array will hold all our task objects
    }
  },
  // Computed properties are like reactive getters
  computed: {
    completedCount() {
      // Count how many tasks are marked as completed
      return this.tasks.filter(task => task.completed).length;
    }
  },
  // Methods are functions that can be called from the template
  methods: {
    addTask() {
      if (this.newTask.trim() !== '') {
        // Add a new task object to the tasks array
        this.tasks.push({
          text: this.newTask,
          completed: false
        });
        // Clear the input field
        this.newTask = '';
      }
    },
    removeTask(index) {
      // Remove a task from the array at a specific index
      this.tasks.splice(index, 1);
    }
  }
}
</script>

<!-- The style section is now removed and moved to a separate file -->