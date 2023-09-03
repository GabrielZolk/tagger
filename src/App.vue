<script setup>
import { computed, onMounted, ref } from "vue";

const text = ref("");
const search = ref("");
const notes = ref([]);
const modal = ref(false);
const actionValue = ref(false);
const selectedNote = ref(null);
const EditModal = ref(false);
const EditedText = ref("");

onMounted(() => {
  const storedNotes = localStorage.getItem("tag");
  if (storedNotes) {
    notes.value = JSON.parse(storedNotes);
  }
});

function openModal() {
  modal.value = true;
}

function actionCall(note) {
  selectedNote.value = note;
  actionValue.value = !actionValue.value;
}

function addNote() {
  if (text.value) {
    notes.value.push({
      id: notes.value.length,
      content: text.value,
    });
    modal.value = false;
    text.value = "";
    localStorage.setItem("tag", JSON.stringify(notes.value));
  } else {
    alert("Please write a tag");
  }
}

function deleteTag(note) {
  const index = notes.value.findIndex((n) => n.id === note.id);
  notes.value.splice(index, 1);
  localStorage.setItem("tag", JSON.stringify(notes.value));
  actionValue.value = false;
}

function editTag(note) {
  EditModal.value = true;
  EditedText.value = note.content;
}

function updateTag(note) {
  const index = notes.value.findIndex((n) => n.id === note.id);
  if (EditedText.value) {
    notes.value[index].content = EditedText.value;
    localStorage.setItem("tag", JSON.stringify(notes.value));
    EditModal.value = false;
  } else {
    alert("Cannot be empty");
  }
}

const filteredNotes = computed(() => {
  if (notes.value && search.value) {
    return notes.value.filter((note) => {
      return note.content.toLowerCase().includes(search.value);
    });
  }
  return notes.value;
});
</script>

<template>
  <div v-if="modal" class="modal-container">
    <div class="modal-content">
      <textarea type="text" v-model="text"></textarea>
      <button @click="addNote">Add Tag</button>
    </div>
  </div>
  <main v-else class="container">
    <div class="actions">
      <span>Search by tag </span>
      <input type="text" v-model="search" />
      <span> or </span>
      <button @click="openModal">Write a tag</button>
    </div>
    <div class="notes">
      <div class="notes-content">
        <p
          v-for="note in filteredNotes"
          :key="note.id"
          @click="actionCall(note)"
        >
          {{ note.content }}
        </p>
      </div>
    </div>
  </main>
  <div class="action-call" v-if="actionValue">
    <button @click="editTag(selectedNote)">Edit</button>
    <button @click="deleteTag(selectedNote)">Delete</button>
    <button @click="() => (actionValue = false)">Close</button>
  </div>
  <div v-if="EditModal" class="modal-container">
    <div class="modal-content">
      <textarea type="text" v-model="EditedText"></textarea>
      <button @click="updateTag(selectedNote)">Update Tag</button>
    </div>
  </div>
</template>

<style scoped>
.modal-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 10px;
  width: 500px;
  height: 300px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.modal-content textarea {
  width: 100%;
  height: 200px;
  padding: 10px;
  font-size: 20px;
  border: none;
  border-radius: 10px;
  outline: none;
  resize: none;
}

.modal-content button {
  width: 100%;
  padding: 10px;
  font-size: 20px;
  border: none;
  border-radius: 10px;
  outline: none;
  cursor: pointer;
  background-color: rgb(0, 0, 0);
  color: white;
  margin-top: 20px;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  width: 100%;
}

.container .actions {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

.actions input {
  padding: 10px;
  font-size: 20px;
  border: none;
  border-radius: 10px;
  outline: none;
  cursor: pointer;
  background-color: rgb(0, 0, 0);
  color: white;
}

.actions button {
  padding: 10px;
  font-size: 20px;
  border: none;
  border-radius: 10px;
  outline: none;
  cursor: pointer;
  background-color: rgb(0, 0, 0);
  color: #fff;
}

.actions span {
  font-size: 25px;
  font-weight: bold;
}

.notes {
  display: flex;
  align-items: center;
  justify-content: center;
}

.notes-content {
  display: flex;
}

.notes p {
  font-size: 20px;
  margin: 5px;
  padding: 10px;
  border-radius: 10px;
  background-color: rgb(0, 0, 0);
  color: white;
  cursor: pointer;
  user-select: none;
  transition: all 0.3s;
  text-align: center;
  max-width: 500px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
}

.action-call {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.action-call h3 {
  color: white;
  font-size: 30px;
  font-weight: bold;
  text-align: center;
  margin-bottom: 20px;
}

.action-call button {
  padding: 10px;
  font-size: 20px;
  border: none;
  border-radius: 10px;
  outline: none;
  cursor: pointer;
  background-color: white;
  color: rgb(0, 0, 0);
  margin-right: 5px;
}
</style>
