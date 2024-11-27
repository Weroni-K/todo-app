<template>
  <div class="todo-app">
    <form @submit.prevent="addTask" class="task-form">
      <input
        v-model="newTask"
        placeholder="Create a new todo..."
        type="text"
        class="task-input"
        maxlength="200"
      />
      <div class="add-and-counter">
        <button type="submit" class="add-task" :class="{ visible: newTask.trim() !== '' }">
          +
        </button>
        <span class="char-counter">{{ newTask.length }}/200</span>
      </div>
    </form>

    <draggable :list="filteredTasks" class="task-list" animation="200">
      <template #item="{ element, index }">
        <div class="task-item" :class="{ completed: element.completed }">
          <label class="checkbox-container">
            <input type="checkbox" v-model="element.completed" /><span class="checkmark"></span>
            <p class="task-item-text">
              {{ element.text }}
            </p></label
          ><button @click="removeTask(index)" class="delete-task"></button>
        </div>
      </template>
    </draggable>

    <div class="task-footer">
      <span class="task-counter">{{ tasksLeft }} items left</span>
      <div class="task-filters desktop-filters">
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

    <div class="task-filters mobile-filters">
      <button @click="setFilter('all')" :class="{ active: filter === 'all' }">All</button>
      <button @click="setFilter('active')" :class="{ active: filter === 'active' }">Active</button>
      <button @click="setFilter('completed')" :class="{ active: filter === 'completed' }">
        Completed
      </button>
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
input,
form,
label {
  font-family: var(--font-body);
  color: var(--color-text-primary);
  font-weight: var(--font-weight-400);
  font-size: 18px;
}

button {
  font-family: var(--font-body);
}

.todo-app {
  width: 100%;
  margin: 0 auto;
  padding: 0;
  display: flex;
  flex-direction: column;
}

.task-form {
  height: 4rem;
  padding-left: 1.5rem;
  display: flex;
  align-items: center;
  gap: 0.5rem;
  border-radius: 6px;
  background-color: var(--color-bg-secondary);
}

.task-input {
  flex-grow: 1;
  border-radius: 6px;
  color: var(--color-primary);
  background-color: var(--color-bg-secondary);
  border: none;
  outline: none;
}

.add-and-counter {
  display: flex;
  flex-direction: column;
}

.add-task {
  height: 2rem;
  justify-content: center;
  align-items: center;
  background: transparent;
  border: none;
  color: var(--color-text-secondary);
  font-size: 2rem;
  cursor: pointer;
  transition:
    opacity 0.3s ease,
    transform 0.2s ease;
}

.add-task.visible {
  display: flex;
  opacity: 1;
  transform: scale(1);
}

.add-task:not(.visible) {
  opacity: 0;
  transform: scale(0.9);
}

.char-counter {
  margin: 0 0.5rem;
  font-size: 12px;
  color: var(--color-text-secondary);
  text-align: right;
  align-self: flex-end;
}

.task-list {
  margin-top: 1.5rem;
  padding: 0;
  background-color: var(--color-bg-secondary);
  border-radius: 6px;
}

.task-item {
  min-height: 4rem;
  margin: 0;
  padding: 0.5rem 0;
  display: flex;
  align-items: center;
  justify-content: space-between;
  border-bottom: 0.1rem solid var(--color-text-secondary-hsla);
  background: transparent;
  cursor: grab;
}

.task-item.completed label {
  color: var(--color-text-secondary);
  text-decoration: line-through;
}

.checkbox-container {
  display: flex;
  align-items: center;
  cursor: pointer;
  font-size: 18px;
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
}

.checkbox-container input {
  height: 0;
  width: 0;
  opacity: 0;
  cursor: pointer;
}

.checkmark {
  position: relative;
  height: 1.5rem;
  margin: auto 1.5rem;
  aspect-ratio: 1;
  background: transparent;
  border-radius: 50%;
  border: var(--color-text-secondary-hsla) solid 1px;
}

.checkbox-container:hover input ~ .checkmark {
  background: linear-gradient(hsl(192, 100%, 67%), hsl(280, 87%, 65%));
}

.checkbox-container input:checked ~ .checkmark {
  background: linear-gradient(hsl(192, 100%, 67%), hsl(280, 87%, 65%));
}

.checkmark:after {
  content: '';
  position: absolute;
  display: none;
}

.task-item-text {
  max-width: 28rem;
}

.checkbox-container input:checked ~ .checkmark:after {
  display: block;
}

.checkbox-container .checkmark:after {
  left: 9px;
  top: 5px;
  width: 6px;
  height: 10px;
  border: solid white;
  border-width: 0 3px 2px 0;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
}

.delete-task {
  position: relative;
  width: 100%;
  max-width: 1.5rem;
  height: 1.5rem;
  margin-inline: 1rem;
  padding: 0;
  background: transparent;
  border: none;
  cursor: pointer;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.delete-task::before,
.delete-task::after {
  content: '';
  position: absolute;
  top: 50%;
  left: 50%;
  width: 100%;
  height: 1px;
  background-color: var(--color-text-secondary);
  border-radius: 1px;
  transform-origin: center;
}

.delete-task::before {
  transform: translate(-50%, -50%) rotate(45deg);
}

.delete-task::after {
  transform: translate(-50%, -50%) rotate(-45deg);
}

.task-item:hover .delete-task {
  opacity: 1;
}

.task-footer {
  height: 3rem;
  padding: 0 1.5rem;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: var(--color-bg-secondary);
  border-radius: 6px;
  font-size: 14px;
}

.task-counter {
  font-size: 14px;
  color: var(--color-text-secondary);
}

.task-filters button {
  padding: 5px 10px;
  background: transparent;
  border: none;
  border-radius: 6px;
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

.desktop-filters {
  display: flex;
  gap: 4px;
}

.mobile-filters {
  display: none;
  margin-top: 1rem;
  justify-content: center;
  gap: 4px;
}

.clear-completed {
  padding: 0;
  background: transparent;
  border: none;
  color: var(--color-text-secondary);
  border-radius: 6px;
  cursor: pointer;
  transition:
    background-color 0.3s ease,
    color 0.3s ease;
}

.clear-completed:hover {
  color: var(--color-text-hover);
}

.bottom-text {
  padding: 2rem 0 2rem 0;
  text-align: center;
  font-size: 14px;
  color: var(--color-text-secondary);
}

@media (max-width: 592px) {
  body {
    font-size: 14px;
  }
  .task-list {
    margin-top: 1rem;
  }
  .task-item {
    min-height: 3.25rem;
  }
  .task-input {
    height: 3rem;
    font-size: 14px;
  }
  .add-task {
    height: 3rem;
    width: 3rem;
  }
  .checkbox-container {
    font-size: 12px;
  }
  .checkmark {
    height: 1.25rem;
    margin-inline: 1.25rem;
  }
  .mobile-filters {
    display: flex;
    background-color: var(--color-bg-secondary);
    height: 3rem;
    border-radius: 6px;
  }
  .desktop-filters {
    display: none;
  }
  .task-footer {
    height: 3.25rem;
    flex-wrap: wrap;
    justify-content: space-between;
    gap: 1rem;
  }

  .checkbox-container .checkmark:after {
    left: 7px;
    top: 4px;
    width: 5px;
    height: 9px;
    border-width: 0 2px 2px 0;
  }
  .delete-task {
    max-width: 1rem;
    height: 1rem;
  }
  .char-counter {
    font-size: 10px;
  }
}
</style>
