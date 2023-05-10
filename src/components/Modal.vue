<script setup>
import { v4 as uuidv4 } from 'uuid';
import { ref, onMounted } from 'vue';

const props = defineProps(['inputModal']);
const emit = defineEmits(['close', 'add-event']);

const errorShow = ref(false);
const eventTitle = ref('');
const inputDate = ref(null);

function close() {
  eventTitle.value = '';
  errorShow.value = false;
  inputDate.value.value = null;
  emit('close');     
}

function addEvent() {
  if (!eventTitle.value || !inputDate.value.value) {
    errorShow.value = true;
  } else {
    let newEvent = {
      id: uuidv4(),
      title: eventTitle.value,
      start: inputDate.value.value
    };
    emit('add-event', newEvent);
    close();
  }
}

onMounted(() => {
  jQuery(inputDate.value).datetimepicker({
    timepicker: true,
    format: 'Y-m-d H:i',
    minDate: 0,
  });
})
</script>

<template>
  <div class="overlay" v-show="inputModal" @click.self="close"></div>
  <form class="modal" v-show="inputModal" @submit.prevent="addEvent">
    <label>
      Event Title
      <input type="text" placeholder="Please enter the event title" v-model.trim="eventTitle"> 
    </label>                 
    <label>
      Event Date
      <input placeholder="Please enter the event date" ref="inputDate">     
    </label>
    <p v-if="errorShow">Must not be empty</p>
    <button type="submit" class="add">submit</button>        
    <button type="button" class="cancel" @click="close">cancel</button>        
  </form>
</template>

<style scoped>
.overlay {
    position: fixed;
    height: 100%;
    width: 100%;
    background-color: var(--overlay);
    z-index: 3;
  }

  .modal {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 308px;
    background-color: var(--modal);
    border-radius: 8px;
    z-index: 4;
    padding: 15px;
  }

  label {
    color: white;
    display: block;
    font-size: 1.2rem;
  }

  label + label {
    margin-top: 10px;
  }

  input{
    display: block;
    width: 100%;
    border: none;
    outline: none;
    font-size: 1.2rem;
    padding: 4px;
    border-radius: 5px;
  }

  button {
    margin-top: 10px;
    width: 50%;
    font-size: 1.4rem;
    padding: 5px 0;
    cursor: pointer;
    border: none;
    outline: none;
    color: #fff;
  }

  p {
    background-color: var(--error);
    color: #fff;
    border-radius: 8px;
    font-size: 1rem;
    padding: 2px 0;
    font-weight: normal;
    text-align: center;
    margin-top: 10px;
  }

  .add {
    background-color: var(--add);
    border-top-left-radius: 5px;
    border-bottom-left-radius: 5px;
  }

  .add:hover {
    background-color: var(--add-hover);
  }

  .cancel {
    background-color: var(--cancel);
    border-top-right-radius: 5px;
    border-bottom-right-radius: 5px;
  }

  .cancel:hover {
    background-color: var(--cancel-hover);
  }
  </style>