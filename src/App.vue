<template>
  <div class="min-h-screen bg-gradient-to-br from-slate-900 via-purple-900 to-slate-900 text-white overflow-hidden relative">
    <!-- Animated Background -->
    <div class="absolute inset-0 opacity-20">
      <div class="absolute inset-0 bg-gradient-to-r from-purple-500/10 to-cyan-500/10 animate-pulse"></div>
      <div class="grid-background"></div>
    </div>

    <!-- Floating Orbs -->
    <div class="floating-orb orb-1"></div>
    <div class="floating-orb orb-2"></div>
    <div class="floating-orb orb-3"></div>

    <div class="relative z-10 p-6">
      <!-- Header -->
      <div class="text-center mb-8">
        <h1 class="text-6xl font-bold text-white drop-shadow-2xl mb-4" style="text-shadow: 0 0 20px rgba(147, 51, 234, 0.5)">
          NEXUS TASKS
        </h1>
        <p class="text-xl text-purple-200">Advanced Task Management System</p>
      </div>

      <!-- Progress Stats -->
      <div class="glass-card p-6 mb-8 max-w-4xl mx-auto">
        <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-4">
          <div class="text-center">
            <div class="text-2xl font-bold text-cyan-400">{{ tasks.length }}</div>
            <div class="text-sm text-gray-300">Total Tasks</div>
          </div>
          <div class="text-center">
            <div class="text-2xl font-bold text-green-400">{{ completedTasks.length }}</div>
            <div class="text-sm text-gray-300">Completed</div>
          </div>
          <div class="text-center">
            <div class="text-2xl font-bold text-purple-400">{{ completionPercentage }}%</div>
            <div class="text-sm text-gray-300">Progress</div>
          </div>
          <div class="text-center">
            <div class="text-2xl font-bold text-yellow-400">{{ totalEstimatedTime }}h</div>
            <div class="text-sm text-gray-300">Est. Time</div>
          </div>
        </div>

        <!-- Progress Bar -->
        <div class="w-full bg-gray-700 rounded-full h-3 overflow-hidden">
          <div
              class="h-full bg-gradient-to-r from-purple-500 to-cyan-500 transition-all duration-500 ease-out"
              :style="{ width: completionPercentage + '%' }"
          ></div>
        </div>
      </div>

      <!-- Task Creation Form -->
      <div class="glass-card p-6 mb-8 max-w-2xl mx-auto">
        <form @submit.prevent="addTask" class="space-y-4">
          <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
            <div>
              <label class="block text-sm font-medium text-purple-200 mb-2">Task Title</label>
              <input
                  v-model="newTask.text"
                  type="text"
                  placeholder="Enter task title..."
                  class="w-full px-4 py-3 bg-gray-800/50 border border-purple-500/30 rounded-lg text-white placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all"
                  required
              />
            </div>
            <div>
              <label class="block text-sm font-medium text-purple-200 mb-2">Time Estimate (hours)</label>
              <input
                  v-model.number="newTask.timeEstimate"
                  type="number"
                  min="0.5"
                  step="0.5"
                  placeholder="2.5"
                  class="w-full px-4 py-3 bg-gray-800/50 border border-purple-500/30 rounded-lg text-white placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all"
              />
            </div>
          </div>

          <div>
            <label class="block text-sm font-medium text-purple-200 mb-2">Description</label>
            <textarea
                v-model="newTask.description"
                placeholder="Task description..."
                rows="3"
                class="w-full px-4 py-3 bg-gray-800/50 border border-purple-500/30 rounded-lg text-white placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all resize-none"
            ></textarea>
          </div>

          <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
            <div>
              <label class="block text-sm font-medium text-purple-200 mb-2">Color</label>
              <select
                  v-model="newTask.color"
                  class="w-full px-4 py-3 bg-gray-800/50 border border-purple-500/30 rounded-lg text-white focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all"
              >
                <option value="purple">Purple</option>
                <option value="blue">Blue</option>
                <option value="green">Green</option>
                <option value="red">Red</option>
                <option value="yellow">Yellow</option>
                <option value="pink">Pink</option>
                <option value="cyan">Cyan</option>
              </select>
            </div>
            <div>
              <label class="block text-sm font-medium text-purple-200 mb-2">Emoji</label>
              <select
                  v-model="newTask.emoji"
                  class="w-full px-4 py-3 bg-gray-800/50 border border-purple-500/30 rounded-lg text-white focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all"
              >
                <option value="🚀">🚀 Rocket</option>
                <option value="💡">💡 Idea</option>
                <option value="⚡">⚡ Energy</option>
                <option value="🎯">🎯 Target</option>
                <option value="🔥">🔥 Fire</option>
                <option value="💎">💎 Diamond</option>
                <option value="🌟">🌟 Star</option>
                <option value="🎨">🎨 Art</option>
                <option value="📊">📊 Chart</option>
                <option value="🔧">🔧 Tool</option>
              </select>
            </div>
            <div>
              <label class="block text-sm font-medium text-purple-200 mb-2">Connect To</label>
              <select
                  v-model="newTask.connectTo"
                  class="w-full px-4 py-3 bg-gray-800/50 border border-purple-500/30 rounded-lg text-white focus:outline-none focus:ring-2 focus:ring-purple-500 focus:border-transparent transition-all"
              >
                <option value="">No Connection</option>
                <option v-for="task in tasks" :key="task.id" :value="task.id">
                  {{ task.emoji }} {{ task.text }}
                </option>
              </select>
            </div>
          </div>

          <button
              type="submit"
              class="w-full py-3 px-6 bg-gradient-to-r from-purple-600 to-cyan-600 hover:from-purple-700 hover:to-cyan-700 rounded-lg font-semibold text-white transition-all duration-300 transform hover:scale-105 hover:shadow-lg hover:shadow-purple-500/25"
          >
            Create Task
          </button>
        </form>
      </div>

      <!-- Task Board -->
      <div class="relative max-w-6xl mx-auto">
        <div
            ref="taskBoard"
            class="relative min-h-96 bg-gray-900/30 rounded-xl border border-purple-500/20 overflow-hidden"
            style="height: 600px;"
            @mousedown="startPanning"
            @mousemove="handlePanning"
            @mouseup="stopPanning"
            @mouseleave="stopPanning"
        >
          <!-- Connection Lines SVG -->
          <svg class="absolute inset-0 w-full h-full pointer-events-none" style="z-index: 1;">
            <defs>
              <linearGradient id="connectionGradient" x1="0%" y1="0%" x2="100%" y2="0%">
                <stop offset="0%" style="stop-color:#8b5cf6;stop-opacity:1" />
                <stop offset="100%" style="stop-color:#06b6d4;stop-opacity:1" />
              </linearGradient>
            </defs>
            <path
                v-for="connection in connections"
                :key="connection.id"
                :d="connection.path"
                stroke="url(#connectionGradient)"
                stroke-width="2"
                fill="none"
                class="animate-pulse"
            />
          </svg>

          <!-- Tasks -->
          <div
              v-for="task in tasks"
              :key="task.id"
              :class="[
              'absolute task-card cursor-move transition-all duration-300 transform hover:scale-105',
              `task-${task.color}`,
              { 'task-completed': task.completed, 'task-creating': task.isCreating }
            ]"
              :style="{
              left: task.x + 'px',
              top: task.y + 'px',
              zIndex: draggedTask?.id === task.id ? 1000 : 10
            }"
              @mousedown="startDragging($event, task)"
          >
            <div class="p-4 w-64">
              <div class="flex items-center justify-between mb-2">
                <div class="flex items-center space-x-2">
                  <span class="text-2xl">{{ task.emoji }}</span>
                  <span class="font-semibold text-white">{{ task.text }}</span>
                </div>
                <button
                    @click="toggleTask(task.id)"
                    :class="[
                    'w-6 h-6 rounded border-2 transition-all duration-300',
                    task.completed
                      ? 'bg-green-500 border-green-500'
                      : 'border-gray-400 hover:border-white'
                  ]"
                >
                  <svg v-if="task.completed" class="w-4 h-4 text-white mx-auto" fill="currentColor" viewBox="0 0 20 20">
                    <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" />
                  </svg>
                </button>
              </div>

              <p v-if="task.description" class="text-sm text-gray-300 mb-2">{{ task.description }}</p>

              <div class="flex items-center justify-between text-xs text-gray-400">
                <span v-if="task.timeEstimate">⏱️ {{ task.timeEstimate }}h</span>
                <button
                    @click="deleteTask(task.id)"
                    class="text-red-400 hover:text-red-300 transition-colors"
                >
                  🗑️
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
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
      newTask: {
        text: '',
        description: '',
        timeEstimate: 1,
        color: 'purple',
        emoji: '🚀',
        connectTo: ''
      },
      tasks: [],
      draggedTask: null,
      dragOffset: { x: 0, y: 0 },
      isPanning: false,
      panStart: { x: 0, y: 0 }
    }
  },
  computed: {
    completedTasks() {
      return this.tasks.filter(task => task.completed);
    },
    completionPercentage() {
      return this.tasks.length > 0 ? Math.round((this.completedTasks.length / this.tasks.length) * 100) : 0;
    },
    totalEstimatedTime() {
      return this.tasks.reduce((total, task) => total + (task.timeEstimate || 0), 0);
    },
    connections() {
      const result = [];
      this.tasks.forEach(task => {
        if (task.connectTo) {
          const connectedTask = this.tasks.find(t => t.id === task.connectTo);
          if (connectedTask) {
            const startX = task.x + 128; // center of task card
            const startY = task.y + 60;
            const endX = connectedTask.x + 128;
            const endY = connectedTask.y + 60;

            result.push({
              id: `${task.id}-${connectedTask.id}`,
              path: `M ${startX} ${startY} Q ${(startX + endX) / 2} ${Math.min(startY, endY) - 50} ${endX} ${endY}`
            });
          }
        }
      });
      return result;
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
        const backendTasks = await response.json();

        // Transform backend tasks to match our new format
        this.tasks = backendTasks.map(task => ({
          id: task.id,
          text: task.text,
          description: '',
          timeEstimate: 1,
          color: 'purple',
          emoji: '🚀',
          connectTo: '',
          completed: task.completed,
          x: Math.random() * 400 + 50,
          y: Math.random() * 300 + 50,
          isCreating: false
        }));
      } catch (error) {
        console.error('Failed to load tasks:', error);
        // Fallback to local storage
        const saved = localStorage.getItem('futuristic-tasks');
        if (saved) {
          this.tasks = JSON.parse(saved);
        }
      }
    },

    // Add task to backend
    async addTask() {
      if (this.newTask.text.trim() !== '') {
        try {
          const response = await fetch(`${API_BASE}/api/tasks`, {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify({
              text: this.newTask.text,
              completed: false
            })
          });

          const backendTask = await response.json();

          // Create our enhanced task object
          const task = {
            id: backendTask.id,
            text: this.newTask.text,
            description: this.newTask.description,
            timeEstimate: this.newTask.timeEstimate,
            color: this.newTask.color,
            emoji: this.newTask.emoji,
            connectTo: this.newTask.connectTo,
            completed: false,
            x: Math.random() * 400 + 50,
            y: Math.random() * 300 + 50,
            isCreating: true
          };

          this.tasks.push(task);

          // Reset form
          this.newTask = {
            text: '',
            description: '',
            timeEstimate: 1,
            color: 'purple',
            emoji: '🚀',
            connectTo: ''
          };

          // Remove creating animation after a delay
          setTimeout(() => {
            const index = this.tasks.findIndex(t => t.id === task.id);
            if (index !== -1) {
              this.tasks[index].isCreating = false;
            }
          }, 500);
        } catch (error) {
          console.error('Failed to add task:', error);
          // Fallback: add locally with enhanced properties
          const task = {
            id: Date.now(),
            text: this.newTask.text,
            description: this.newTask.description,
            timeEstimate: this.newTask.timeEstimate,
            color: this.newTask.color,
            emoji: this.newTask.emoji,
            connectTo: this.newTask.connectTo,
            completed: false,
            x: Math.random() * 400 + 50,
            y: Math.random() * 300 + 50,
            isCreating: true
          };

          this.tasks.push(task);

          // Reset form
          this.newTask = {
            text: '',
            description: '',
            timeEstimate: 1,
            color: 'purple',
            emoji: '🚀',
            connectTo: ''
          };

          // Save to local storage
          localStorage.setItem('futuristic-tasks', JSON.stringify(this.tasks));

          // Remove creating animation after a delay
          setTimeout(() => {
            const index = this.tasks.findIndex(t => t.id === task.id);
            if (index !== -1) {
              this.tasks[index].isCreating = false;
            }
          }, 500);
        }
      }
    },

    // Toggle task completion status
    async toggleTask(id) {
      const task = this.tasks.find(t => t.id === id);
      if (task) {
        try {
          // Update backend
          await fetch(`${API_BASE}/api/tasks/${id}`, {
            method: 'PUT',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify({
              text: task.text,
              completed: !task.completed
            })
          });

          // Update local state
          task.completed = !task.completed;
        } catch (error) {
          console.error('Failed to update task:', error);
          // Fallback: update locally
          task.completed = !task.completed;
          localStorage.setItem('futuristic-tasks', JSON.stringify(this.tasks));
        }
      }
    },

    // Remove task from backend
    async deleteTask(id) {
      try {
        await fetch(`${API_BASE}/api/tasks/${id}`, {
          method: 'DELETE'
        });
        this.tasks = this.tasks.filter(t => t.id !== id);
      } catch (error) {
        console.error('Failed to delete task:', error);
        // Fallback: remove locally
        this.tasks = this.tasks.filter(t => t.id !== id);
        localStorage.setItem('futuristic-tasks', JSON.stringify(this.tasks));
      }
    },

    // Dragging functionality
    startDragging(event, task) {
      event.preventDefault();
      this.draggedTask = task;
      const rect = this.$refs.taskBoard.getBoundingClientRect();
      this.dragOffset = {
        x: event.clientX - rect.left - task.x,
        y: event.clientY - rect.top - task.y
      };

      document.addEventListener('mousemove', this.handleDragging);
      document.addEventListener('mouseup', this.stopDragging);
    },

    handleDragging(event) {
      if (!this.draggedTask) return;

      const rect = this.$refs.taskBoard.getBoundingClientRect();
      this.draggedTask.x = Math.max(0, Math.min(rect.width - 256, event.clientX - rect.left - this.dragOffset.x));
      this.draggedTask.y = Math.max(0, Math.min(rect.height - 120, event.clientY - rect.top - this.dragOffset.y));
    },

    stopDragging() {
      if (this.draggedTask) {
        // Save position changes
        localStorage.setItem('futuristic-tasks', JSON.stringify(this.tasks));
        this.draggedTask = null;
      }
      document.removeEventListener('mousemove', this.handleDragging);
      document.removeEventListener('mouseup', this.stopDragging);
    },

    startPanning(event) {
      if (event.target === this.$refs.taskBoard) {
        this.isPanning = true;
        this.panStart = { x: event.clientX, y: event.clientY };
      }
    },

    handlePanning(event) {
      if (this.isPanning) {
        // Panning logic can be implemented here if needed
      }
    },

    stopPanning() {
      this.isPanning = false;
    }
  }
}
</script>

<style scoped>
/* Component-specific styles only */
</style>