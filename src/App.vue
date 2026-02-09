<template>
  <div class="quiz-app">
    <div class="card">
      <h1 class="main-title">Bienvenue <br> dans To-do-list</h1>
      <p class="subtitle">Cette application vous permet de gérer vos tâches quotidiennes.</p>

      <div v-if="tasks.length > 1 && tasksDone.length <= tasksNotDone.length" class="carte">
        <div class="carte-child">
          <transition name="fade">
            <button @click="shuffleTasks" class="btn">Mélanger</button>
          </transition>
        </div>
      </div>

      <form @submit.prevent="addTask" class="task-form">
        <div class="input-section">
          <input
            type="text"
            v-model="newTask"
            placeholder="Ajouter une tâche..."
            required
          >
          <button
            type="submit"
            :disabled="newTask.trim() === ''"
          >Ajouter</button>
        </div>
      </form>

      <div class="task-list">
        <TransitionGroup name="fade" tag="ul">
          <li
            v-for="task in tasksNotDone"
            :key="task.id"
            class="task-item"
          >
            <input type="checkbox" v-model="task.terminer" class="custom-checkbox" />
            <span>{{ task.name }}</span>
            <button @click="removeTask(task.id)" class="btn-delete">Supprimer</button>
          </li>

          <li
            v-for="task in tasksDone"
            :key="'done-' + task.id"
            class="task-item fini"
          >
            <input type="checkbox" v-model="task.terminer" class="custom-checkbox" />
            <span class="terminer">{{ task.name }}</span>
            <span class="finished">(terminée)</span>
            <button @click="removeTask(task.id)" class="btn-delete">Supprimer</button>
          </li>
        </TransitionGroup>

        <div v-if="tasks.length > 0" class="summary">
          <p>{{ tasksCount }} tâche{{ tasksCount > 1 ? 's' : '' }} restante{{ tasksCount > 1 ? 's' : '' }}.</p>
        </div>

        <p v-else class="summary">Aucune tâche à afficher.</p>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

const tasks = ref([
  { id: 1, name: "Tâche 1", terminer: false },
])

const newTask = ref('')

const addTask = () => {
  if (newTask.value.trim() === '') return
  tasks.value.push({ id: Date.now(), name: newTask.value.trim(), terminer: false })
  newTask.value = ''
}

const removeTask = (id) => {
  tasks.value = tasks.value.filter(task => task.id !== id)
}

const tasksNotDone = computed(() => tasks.value.filter(task => !task.terminer))
const tasksDone = computed(() => tasks.value.filter(task => task.terminer))
const tasksCount = computed(() => tasksNotDone.value.length)
const shuffleTasks = () => {
  tasks.value.sort(() => Math.random() - 0.5)
}
</script>

<style scoped>
.quiz-app {
  background: #f3f1f1;
  display: flex;
  justify-content: center;
  padding: 50px 15px;
  font-family: 'Segoe UI', sans-serif;
}

.card {
  background-color: #f1eaea;
  padding: 30px;
  border-radius: 20px;
  width: 100%;
  max-width: 500px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  border: solid 1px #000000;
}

.main-title {
  font-size: 28px;
  font-weight: 800;
  text-align: center;
  margin-bottom: 10px;
  color: #1c1c1c;
}

.subtitle {
  text-align: center;
  margin-bottom: 25px;
  font-size: 16px;
  color: #333;
}

.input-section {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

input[type="text"] {
  flex: 1;
  padding: 10px 15px;
  border-radius: 8px;
  border: 1px solid #ccc;
  font-size: 16px;
}

button[type="submit"] {
  background-color: #6c63ff;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 8px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s;
}
button[type="submit"]:disabled {
  background-color: #6b63ffa9;
  cursor: not-allowed;
}

button[type="submit"]:hover {
  background-color: #574bdb;
}
button[type="submit"]:hover:disabled {
  background-color: #6b63ffa9;
}

/* Carte Styles */
.carte {
  padding: 3px;
  border-radius: 10px;
  background-color: #f1eaea;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  position: absolute;
  top: 40%;
  right: 100px;
  border: 1px solid black;
  transform: rotateZ(10deg);
  transition: transform 0.4s ease;
}

.carte-child {
  padding: 30px;
  background-color: #f1eaea;
  border: 1px solid black;
  border-radius: 10px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  transform: rotateZ(-10deg);
  transition: transform 0.4s ease;
}

.carte:hover {
  transform: rotateZ(-10deg);
}

.carte:hover .carte-child {
  transform: rotateZ(40deg);
}
.carte:hover .btn,
.carte-child:hover .btn {
  transform: rotate(-20deg);
  transition: transform 0.3s ease;
}


.carte:hover .btn {
  transform: rotateZ(-30deg);
  transition: transform 0.5s ease;
}

/* Animation bouton bounce */
@keyframes bounce {
  0%, 100% {
    transform: translateY(0);
  }
  30% {
    transform: translateY(-10px);
  }
  50% {
    transform: translateY(0);
  }
  70% {
    transform: translateY(-5px);
  }
  90% {
    transform: translateY(0);
  }
}

.btn {
  border: solid 1px #000000;
  background-color: #6c63ff;
  color: white;
  padding: 10px 20px;
  border-radius: 8px;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.3s ease;
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
  animation: bounce 3s ease infinite;
}

.btn:hover {
  animation: none;
}

/* Liste des tâches */
.task-list ul {
  list-style: none;
  padding: 0;
}

.task-item {
  background-color: #f9f3f3;
  border: 1px solid #ddd;
  border-radius: 10px;
  padding: 10px 15px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.05);
}

.fini {
  background-color: rgba(0, 255, 34, 0.1);
  border: 1px solid #aaffaa;
}

.finished {
  color: green;
  font-weight: 600;
}

.terminer {
  text-decoration: line-through;
  color: gray;
}

.custom-checkbox {
  appearance: none;
  background-color: #fff;
  border: 2px solid #6c63ff;
  border-radius: 6px;
  width: 20px;
  height: 20px;
  cursor: pointer;
  margin-right: 10px;
  transition: all 0.3s ease-in-out;
}

.custom-checkbox:checked {
  background-color: #6c63ff;
  box-shadow: 0 1px 5px #6c63ff;
}

.btn-delete {
  background: #ff6b6b;
  border: none;
  color: white;
  padding: 6px 12px;
  border-radius: 6px;
  cursor: pointer;
  font-size: 14px;
}

.btn-delete:hover {
  background-color: #e04b4b;
}

.summary {
  text-align: center;
  margin-top: 15px;
  font-weight: bold;
  color: #444;
}

/* Transitions */
.fade-move, .fade-enter-active, .fade-leave-active {
  transition: all 0.5s ease;
}

.fade-enter-from, .fade-leave-to {
  opacity: 0;
  transform: translateY(10px);
}

/* Media Queries for Responsive Design */
@media (max-width: 768px) {
  /* Hide the shuffle button on tablets and mobile */
  .carte {
    display: none;
  }

  .quiz-app {
    padding: 30px 15px;
  }

  .card {
    padding: 20px;
  }

  .main-title {
    font-size: 24px;
  }

  .subtitle {
    font-size: 15px;
  }
}

@media (max-width: 480px) {
  .quiz-app {
    padding: 20px 10px;
  }

  .card {
    padding: 15px;
  }

  .main-title {
    font-size: 22px;
    margin-bottom: 8px;
  }

  .subtitle {
    font-size: 14px;
    margin-bottom: 20px;
  }

  .input-section {
    gap: 8px;
  }

  input[type="text"] {
    padding: 8px 12px;
    font-size: 14px;
  }

  button[type="submit"] {
    padding: 8px 16px;
    font-size: 14px;
  }

  .task-item {
    padding: 8px 10px;
    gap: 8px;
  }

  .task-item span {
    font-size: 14px;
  }

  .btn-delete {
    padding: 5px 10px;
    font-size: 12px;
  }

  .custom-checkbox {
    width: 18px;
    height: 18px;
    margin-right: 8px;
  }

  .finished {
    font-size: 12px;
  }

  .summary {
    font-size: 14px;
  }
}
</style>
