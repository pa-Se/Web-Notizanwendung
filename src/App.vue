<script setup lang="ts">
import { ref } from "vue"

interface Note {
  id: number;
  text: string;
  date: Date;
  backgroundColor: string;
}

const showModal = ref(false)
const newNote = ref("");
const notes = ref<Note[]>([]);
const errorMessage = ref("");
const touching = ref(false);
const intervalId = ref(0);
const timer = ref(0);

function getRandomColor() {
  return "hsl(" + Math.random() * 360 + ", 100%, 75%)";
}

const addNote = () => {
  if (newNote.value.length < 10) {
    return errorMessage.value = "Bitte mehr als 10 Zeichen eingeben.";
  }
  notes.value.push({
    id: Math.floor(Math.random() * 1000000),
    text: newNote.value,
    date: new Date(),
    backgroundColor: getRandomColor(),
  })
  showModal.value = false;
  newNote.value = "";
  errorMessage.value = "";
}

function startTimer(note) {
  intervalId.value = setInterval(() => {
    timer.value++;
    if (timer.value >= 0.5) {
      clearInterval(intervalId.value);
      //timer.value = 0;
      setTimeout(() => {
        deleteObject(note);
      }, 500);
    }
  }, 1000);
}

function stopTimer() {
  clearInterval(intervalId.value);
  intervalId.value = 0;
  timer.value = 0;
}

function deleteObject(note) {
  const index = notes.value.indexOf(note);
  if (index !== -1) {
    notes.value.splice(index, 1);
  }
}

</script>

<template>
  <div v-show="showModal" class="overlay">
    <div class="modal">
      <textarea v-model.trim="newNote" name="note" id="note" cols="30" rows="10">
      </textarea>
      <div v-if="errorMessage">{{ errorMessage }}</div>
      <button @click="addNote()">Notiz hinzufügen</button>
      <button @click="showModal = false" class="closebutton">Schließen</button>
    </div>
  </div>

  <header>
    <h1>Notizen</h1>
    <button @click="showModal = true">+</button>
  </header>

  <div class="cards_container">
    <div v-for="note in notes" class="card" :key="note.id" @pointerdown="startTimer(note)" @pointerup="stopTimer()"
      :style="{ backgroundColor: note.backgroundColor }">
      <p class="main_text"> {{ note.text }} </p>
      <p class="date">{{ note.date.toLocaleDateString("en-GB") }}</p>
    </div>
  </div>
</template>

<style scoped>
header {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  line-height: 1.5;
  max-height: 100vh;
  margin: 0 1rem;
}

h1 {
  font-weight: bolder;
  font-size: 50px;
}

header button {
  border: none;
  padding: 10px;
  width: 50px;
  height: 50px;
  cursor: pointer;
  border-radius: 100%;
  background-color: black;
  color: white;
  font-size: 20px;
  margin-top: 1rem;
}

.cards_container {
  display: flex;
  flex-wrap: wrap;
  margin: 2rem 1rem;
}

.card {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  width: 200px;
  height: 200px;
  background-color: green;
  padding: 10px;
  border-radius: 15px;
  margin-right: 20px;
}

.card .date {
  font-size: 14px;
  font-weight: bolder;
}

.overlay {
  position: absolute;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal {
  width: 500px;
  background-color: white;
  border-radius: 10px;
  padding: 30px;
  display: flex;
  position: relative;
  flex-direction: column;
}

.modal button {
  padding: 10px 20px;
  font-size: 1rem;
  width: 100%;
  background-color: pink;
  cursor: pointer;
  margin-top: 10px;
}

.modal .closebutton {
  padding: 10px 20px;
  font-size: 1rem;
  width: 100%;
  background-color: rgba(0, 0, 0, 0.2);
  cursor: pointer;
  margin-top: 10px;
}


@media (min-width: 1024px) {}
</style>
