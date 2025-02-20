<script setup>
import { defineProps, defineEmits } from 'vue';

const props = defineProps({
  message: {
    type: String,
    default: 'Successfully'
  },
  iconSrc: {
    type: String,
    default: '../asset/icon/iconSuccess.png'
  }
});

const emit = defineEmits(['close']);

const closeLoginModal = () => {
  emit('close');
};
</script>


<template>
  <div class="modal">
    <div class="modal-content">
      <img src="../asset/icon/iconSuccess.png" alt="Success" class="checkmark me-2" />
      <p class="modal-message my-auto">{{ message }}</p>
      <i class="fa-solid fa-xmark close ms-5" @click="closeLoginModal"></i>
    </div>
  </div>
</template>

<style scoped>


.checkmark {
  width: 10%;
  height: auto;
}


/* Modal Styles */
.modal {
  display: flex;
  justify-content: center;
  align-items: start;
  position: fixed;
  z-index: 99;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
}

.modal-content {
  display: flex; /* Use flexbox for alignment */
  flex-direction: row; /* Stack items vertically */
  align-items: center; /* Center items horizontally */
  justify-content: center;
  background-color: #ffffff; /* White background */
  margin: 2% auto;
  padding: 20px;
  border: none; /* No border */
  max-width: 400px; /* Adjusted for a more compact modal */
  text-align: center;
  border-radius: 10px; /* Rounded corners */
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2); /* Shadow for depth */
}


.modal-message {
  font-size: 15px; /* Adjust font size for better readability */
  color: #333; /* Darker text for contrast */
  font-weight: 600;
}

.close {
  color: #aaa;
  font-size: 28px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}
</style>