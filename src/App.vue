<!-- ================= template ================= -->
<template>
  <h1>
    <svg viewBox="0 -17.5 256 256" xmlns="http://www.w3.org/2000/svg" preserveAspectRatio="xMinYMin meet"
      fill="#000000">
      <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
      <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
      <g id="SVGRepo_iconCarrier">
        <path d="M204.8 0H256L128 220.8 0 0h97.92L128 51.2 157.44 0h47.36z" fill="#41B883"></path>
        <path d="M0 0l128 220.8L256 0h-51.2L128 132.48 50.56 0H0z" fill="#41B883"></path>
        <path d="M50.56 0L128 133.12 204.8 0h-47.36L128 51.2 97.92 0H50.56z" fill="#35495E"></path>
      </g>
    </svg>
    Todolist
  </h1>
  <form action="" @submit.prevent="addTask">
    <input type="text" v-model="taskName" placeholder="Ajouter une tâche">
    <button type="submit">
      <svg fill="#ffffff" viewBox="0 0 1920 1920" xmlns="http://www.w3.org/2000/svg" stroke="#ffffff">
        <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
        <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
        <g id="SVGRepo_iconCarrier">
          <path d="M915.744 213v702.744H213v87.842h702.744v702.744h87.842v-702.744h702.744v-87.842h-702.744V213z"
            fill-rule="evenodd"></path>
        </g>
      </svg>
      <span class="only-desktop">Ajouter</span>
    </button>
  </form>

  <div class="todolist-infos">
    <div class="todolist-infos-container">
      <span>Nb de tâches : {{ tasks.length }}</span>
      <span>Nb de tâches terminées : {{ completedTasks }}</span>
    </div>
  </div>

  <ul>
    <li v-for="(task, index) in filteredTasks" :key="index">
      <div class="task">
        <input type="checkbox" :checked="task.done" @change="checkTask(index)">
        <span :class="{ done: task.done }">{{ task.name }}</span>
      </div>
      <div>
        <button @click="deleteTask(index)" class="deleteTaskButton">
          <svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <g id="SVGRepo_bgCarrier" stroke-width="0"></g>
            <g id="SVGRepo_tracerCarrier" stroke-linecap="round" stroke-linejoin="round"></g>
            <g id="SVGRepo_iconCarrier">
              <path
                d="M4 6H20M16 6L15.7294 5.18807C15.4671 4.40125 15.3359 4.00784 15.0927 3.71698C14.8779 3.46013 14.6021 3.26132 14.2905 3.13878C13.9376 3 13.523 3 12.6936 3H11.3064C10.477 3 10.0624 3 9.70951 3.13878C9.39792 3.26132 9.12208 3.46013 8.90729 3.71698C8.66405 4.00784 8.53292 4.40125 8.27064 5.18807L8 6M18 6V16.2C18 17.8802 18 18.7202 17.673 19.362C17.3854 19.9265 16.9265 20.3854 16.362 20.673C15.7202 21 14.8802 21 13.2 21H10.8C9.11984 21 8.27976 21 7.63803 20.673C7.07354 20.3854 6.6146 19.9265 6.32698 19.362C6 18.7202 6 17.8802 6 16.2V6M14 10V17M10 10V17"
                stroke="#ffffff" stroke-width="1.128" stroke-linecap="round" stroke-linejoin="round"></path>
            </g>
          </svg>
          <span class="only-desktop">Supprimer</span>
        </button>
      </div>
    </li>
  </ul>

  <div class="hideCompletedTasks" v-show="showHideCompleted">
    <input type="checkbox" @change="hideCompletedTasks">
    <span>Masquer les tâches terminées</span>
  </div>

</template>


<!-- ================= script ================= -->
<script setup>

import { ref, computed, onMounted, watch } from 'vue';

const taskName = ref('');
const tasks = ref([]);
const hideCompleted = ref(false);

onMounted(() => {
  const savedTasks = localStorage.getItem('tasks');
  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks);
  }
});


watch(tasks, (newTasks) => {
  localStorage.setItem('tasks', JSON.stringify(newTasks));
}, { deep: true });

const savedHideCompleted = localStorage.getItem('hideCompleted');
if (savedHideCompleted !== null) {
  hideCompleted.value = JSON.parse(savedHideCompleted);
}


const addTask = () => {
  if (taskName.value.trim() !== '') {
    tasks.value.push({
      name: taskName.value,
      done: false
    });
    taskName.value = '';
  }

}

const checkTask = (index) => {
  tasks.value[index].done = !tasks.value[index].done;
}

const deleteTask = (index) => {
  tasks.value.splice(index, 1);
}

const showHideCompleted = computed(() => {
  return tasks.value.length > 0 && tasks.value.some(task => task.done);
});

const hideCompletedTasks = (event) => {
  hideCompleted.value = event.target.checked;
  localStorage.setItem('hideCompleted', JSON.stringify(hideCompleted.value));
};

const filteredTasks = computed(() =>
  hideCompleted.value
    ? tasks.value.filter(task => !task.done)
    : tasks.value
);

const completedTasks = computed(() =>
  tasks.value.filter(task => task.done).length
);

</script>


<!-- ================= style ================= -->
<style>
:root {
  --primary-color: #41D392;
  --error-accent: rgb(255, 97, 97);
}

* {
  box-sizing: border-box;
}


body {
  background-color: #1e1e2f;
  color: #f0f0f0;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  padding: 2rem;
  line-height: 1.6;
}

h1 {
  text-align: center;
  color: var(--primary-color);
  margin-bottom: 1.5rem;
}

form {
  justify-content: center;
  margin-bottom: 2rem;
  display: flex;
}

input[type="text"] {
  padding: 0.6rem 1rem;
  border: none;
  width: 300px;
  font-size: 1rem;
  border-radius: 0;
}

button {
  background-color: var(--primary-color);
  color: white;
  border: none;
  padding: 0.6rem 1.2rem;
  cursor: pointer;
  font-size: 1rem;
  transition: background-color 0.2s;
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 6px;
}

button:hover {
  background-color: var(--primary-color);
}

.todolist-infos {
  display: flex;
  align-items: center;
  justify-content: center;
}

.todolist-infos-container {
  width: 500px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding-bottom: 6px;
}


.todolist-infos-container > span {
  display: block;
  color: rgba(255, 255, 255, 0.496);
  font-size: 14px;
}

ul {
  list-style: none;
  padding: 0;
  max-width: 500px;
  margin: 0 auto;
  max-height: 420px;
  overflow: auto;
  border-top: 1px solid rgba(255, 255, 255, 0.199);
}

li {
  background-color: #2c2c3e;
  padding: 0.8rem 1rem;
  margin-bottom: 0.5rem;
  display: flex;
  align-items: center;
  gap: 0.75rem;
  transition: background-color 0.2s;
}

li:hover {
  background-color: #3c3c5c;
}

input[type="checkbox"] {
  accent-color: var(--primary-color);
  transform: scale(1.6);
  cursor: pointer;
  margin-right: 1em;
}

.done {
  text-decoration: line-through;
  color: #999;
}

.task {
  flex-grow: 1;
  display: grid;
  grid-template-columns: auto 1fr auto;
  width: 100%;
}

.task > span {
  overflow: hidden;  
  text-overflow: ellipsis;  
  white-space: nowrap;      
}

.deleteTaskButton {
  padding: 5px 15px 5px 10px;
  background-color: var(--error-accent);
}

.deleteTaskButton:hover {
  background-color: var(--error-accent);
}

svg {
  height: 26px;
  width: 26px;
}

.hideCompletedTasks {
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 1em;
  padding-top: 1.4em;
}

@media screen and (max-width: 425px) {
  #app {
    display: flex;
    flex-direction: column;
    height: 100vh;
  }

  body {
    padding: 12px;
  }

  h1 {
    margin-top: 0;
  }

  .only-desktop {
    display: none;
  }

  .todolist-infos-container {
    display: flex;
    flex-direction: column;
  }

  ul {
    width: 100%;
    flex-grow: 1;
  }

  li {
    padding: 0.6rem 0.8rem
  }

  .deleteTaskButton {
    padding: 5px 9px;
  }
}

</style>