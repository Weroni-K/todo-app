<template>
  <div class="todo-app">
    <form @submit.prevent="addTask" class="task-form">
      <input v-model="newTask" placeholder="Create a new todo..." type="text" class="task-input" />
      <button type="submit" class="add-task" :class="{ visible: newTask.trim() !== '' }">+</button>
    </form>

    <draggable :list="filteredTasks" class="task-list" animation="200">
      <template #item="{ element, index }">
        <div class="task-item" :class="{ completed: element.completed }">
          <label class="checkbox-container">
            <input type="checkbox" v-model="element.completed" /><span class="checkmark"></span>
            {{ element.text }}
          </label>
          <button @click="removeTask(index)" class="delete-task">X</button>
        </div>
      </template>
    </draggable>

    <div class="task-footer">
      <span class="task-counter">{{ tasksLeft }} items left</span>
      <div class="task-filters">
        <button @click="setFilter('all')" :class="{ active: filter === 'all' }">All</button>
        <button @click="setFilter('active')" :class="{ active: filter === 'active' }">
          Active
        </button>
        <button @click="setFilter('completed')" :class="{ active: filter === 'completed' }">
          Completed
        </button>
      </div>
      <button @click="clearCompleted" class="clear-completed">Clear Completed</button>
    </div>

    <p class="bottom-text">Drag and drop to reorder list</p>
  </div>
</template>

<script setup>
import { ref, computed, watch } from 'vue'
import Draggable from 'vuedraggable'

const tasks = ref(JSON.parse(localStorage.getItem('tasks')) || [])
const newTask = ref('')
const filter = ref('all')

const addTask = () => {
  if (newTask.value.trim()) {
    tasks.value.push({ text: newTask.value, completed: false })
    newTask.value = ''
  }
}

const removeTask = (index) => {
  const taskToRemove = filteredTasks.value[index]
  const taskIndex = tasks.value.findIndex((task) => task === taskToRemove)
  tasks.value.splice(taskIndex, 1)
}

const clearCompleted = () => {
  tasks.value = tasks.value.filter((task) => !task.completed)
}

const tasksLeft = computed(() => tasks.value.filter((task) => !task.completed).length)

const filteredTasks = computed(() => {
  if (filter.value === 'active') {
    return tasks.value.filter((task) => !task.completed)
  }
  if (filter.value === 'completed') {
    return tasks.value.filter((task) => task.completed)
  }
  return tasks.value
})

const setFilter = (value) => {
  filter.value = value
}

watch(
  tasks,
  (newTasks) => {
    localStorage.setItem('tasks', JSON.stringify(newTasks))
  },
  { deep: true },
)
</script>

<style scoped>
.todo-app {
  width: 100%;
  margin: 0 auto;
  padding: 0;
  display: flex;
  flex-direction: column;
}

.task-list {
  margin-top: 20px;
  padding: 0;
  list-style: none;
  background-color: var(--color-bg-secondary);
  border-radius: 4px;
}

.task-item {
  height: 4rem;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 8px 0;
  margin: 0;
  border-bottom: 0.1rem solid var(--color-text-secondary);
  background: transparent;
  cursor: grab;
}

.task-item.completed {
  color: var(--color-text-secondary);
  label {
    color: var(--color-text-secondary);

    text-decoration: line-through;
  }
}

.drag-handle {
  cursor: grab;
  margin-right: 10px;
  color: var(--color-text-secondary);
}

.task-form {
  display: flex;
  align-items: center;
  gap: 10px;
  margin-bottom: 10px;
  border-radius: 4px;
  background-color: var(--color-bg-secondary);
}

.task-input {
  flex-grow: 1;
  height: 4rem;
  padding: 10px 20px;
  border-radius: 4px;
  color: var(--color-primary);
  background-color: var(--color-bg-secondary);
  border: none;
  outline: none;
}

.add-task {
  height: 4rem;
  width: 4rem;
  background: transparent;
  color: white;
  border: none;
  border-radius: 50%;
  font-size: 2rem;
  font-weight: 100;
  display: none; /* Hide by default */
  justify-content: center;
  align-items: center;
  cursor: pointer;
  transition:
    opacity 0.3s ease,
    transform 0.2s ease;
}

.add-task.visible {
  display: flex; /* Show button when input has content */
  opacity: 1;
  transform: scale(1); /* Normal size */
}

.add-task:not(.visible) {
  opacity: 0;
  transform: scale(0.9); /* Slightly shrink */
}

/* Hide delete button by default */
.delete-task {
  background: transparent;
  color: var(--color-text-secondary);
  border: none;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
  opacity: 0; /* Hide the button */
  transition: opacity 0.3s ease; /* Smooth transition */
}

/* Show delete button on hover (applies to all task items) */
.task-item:hover .delete-task {
  opacity: 1; /* Make the button visible */
}

/* checkbox styling */
.checkbox-container {
  display: block;
  position: relative;
  padding-left: 3.5rem;
  cursor: pointer;
  font-size: 18px;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

/* Hide the browser's default checkbox */
.checkbox-container input {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
}

/* Create a custom checkbox */
.checkmark {
  position: absolute;
  top: -4px;
  left: 0;
  height: 24px;
  width: 24px;
  background: transparent;
  border-radius: 50%;
  border: var(--color-primary) solid 1px;
  margin-inline: 1rem;
}

/* On mouse-over, add a grey background color */
.checkbox-container:hover input ~ .checkmark {
  background: linear-gradient(hsl(192, 100%, 67%), hsl(280, 87%, 65%));
}

/* When the checkbox is checked, add a blue background */
.checkbox-container input:checked ~ .checkmark {
  background: linear-gradient(hsl(192, 100%, 67%), hsl(280, 87%, 65%));
}

/* Create the checkmark/indicator (hidden when not checked) */
.checkmark:after {
  content: '';
  position: absolute;
  display: none;
}

/* Show the checkmark when checked */
.checkbox-container input:checked ~ .checkmark:after {
  display: block;
}

/* Style the checkmark/indicator */
.checkbox-container .checkmark:after {
  left: 9px;
  top: 5px;
  width: 5px;
  height: 10px;
  border: solid white;
  border-width: 0 3px 3px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}

.bottom-text {
  text-align: center;
  padding: 2rem 0 2rem 0;
  font-size: 16px;
}

.task-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  height: 4rem;
  padding: 0 1rem;
  background-color: var(--color-bg-secondary);
  border-radius: 5px;
  font-size: 14px;
}

.task-counter {
  font-size: 1rem;
  color: var(--color-text-secondary);
}

.task-filters {
  display: flex;
  gap: 4px;
}

.task-filters button {
  background: transparent;
  border: none;
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
  color: var(--color-text-secondary);
  font-weight: var(--font-weight-700);
  transition:
    background-color 0.3s ease,
    color 0.3s ease;
}

.task-filters button:hover {
  color: var(--color-text-hover);
}

.task-filters button.active {
  background-color: var(--color-primary);
  color: var(--color-text-active);
}

.clear-completed {
  background: transparent;
  border: none;
  color: var(--color-text-secondary);
  padding: 5px 10px;
  border-radius: 5px;
  cursor: pointer;
  transition:
    background-color 0.3s ease,
    color 0.3s ease;
}

.clear-completed:hover {
  color: var(--color-text-hover);
}
</style>
