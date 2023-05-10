<script setup>
import { onMounted, ref, watch } from 'vue';
import modal from './components/Modal.vue';

const inputModal = ref(false);
const events = ref([]);
const calendar = ref(null);
let calendarEl

function closeModal() {
  inputModal.value = false;
}

function showModal() {
  inputModal.value = true;
}

function addEvent(newEvent) {
  events.value.push(newEvent);
  calendarEl.addEvent(newEvent);
}

function delEvent(e) {
  let delWarn = confirm('是否刪除此筆活動？');
  if (delWarn) {
    const index = events.value.findIndex(event => event.id === e.event.id);
    events.value.splice(index, 1);
    e.event.remove();
  }
}

watch(
  events,
  value => localStorage.setItem('events', JSON.stringify(value)),
  { deep: true }
)

onMounted(() => {
  calendarEl = new FullCalendar.Calendar(calendar.value, {
    initialView: 'dayGridMonth',
    headerToolbar: {
      start: 'addEventButton today',
      center: 'title',
      end: 'prev next'
    },
    customButtons: {
      addEventButton: {
        text: 'add event',
        click: showModal,
      }
    },
    events: events.value = JSON.parse(localStorage.getItem('events')) || [],
    eventClick: delEvent
  });
  calendarEl.render();
})
</script>

<template>
  <div class="calendar" ref="calendar"></div>
  <modal @close="closeModal" @add-event="addEvent" :input-modal="inputModal"></modal>
</template>

<style scoped>
.calendar {
  position: absolute;
  inset: 0;
  height: 640px;
  width: 640px;
  margin: auto;
  background: white;
  padding: 20px;
  border-radius: 20px;    
  box-shadow: 6px 6px 20px #000;
  overflow: hidden;
}
</style>