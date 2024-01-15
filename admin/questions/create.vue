<template>
    <div>
        <div class="actions">
        <button class="action-btn view" @click="back()">Back</button>
        </div> 
        <p v-if="successMessage" class="success">{{ successMessage }}</p>
        <p v-if="formErrors.general" class="error">{{ formErrors.general }}</p>

      <form @submit.prevent="submitForm" class="simple-form">
        <label for="question">Question:</label>
        <input type="text" id="question" v-model="formData.question" class="form-field">
        <span class="error" v-if="formErrors.question">{{ formErrors.question }}</span>
  
        <input type="submit" value="Submit" class="submit-button">
      </form>
    </div>
  </template>

  <script>
import { ref } from 'vue';

export default {
  setup() {
    definePageMeta({
        layout: 'admin-layout',
      });

    const formData = {
      question: '',
    };

    const formErrors = ref({});
    const successMessage = ref('');

    const submitForm = async () => {
      try {
        const response = await fetch('http://127.0.0.1:8000/api/questions/1', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(formData),
        });

        if (response.ok) {
            formErrors.value = {};
            successMessage.value = 'Form submitted successfully!';
        } else {
          const responseData = await response.json();
          if (responseData && responseData.errors) {
            formErrors.value = responseData.errors;
          } else {
            console.error('Unexpected error format:', responseData);
          }
        }
      } catch (error) {
        console.error('Error submitting form:', error);
      }
    };

    const back = () => {
      this.$router.go(-1);
    };

    return { formData, formErrors, successMessage, submitForm, back };
  },

  methods: {
    back() {
        this.$router.push('/admin/questions');
    },
  }
};
</script>
  
<style scoped>
  .simple-form {
    max-width: 250px;
    margin: auto;
  }

  .form-field {
    width: calc(100% - 12px);
    padding: 8px;
    margin-bottom: 10px;
    box-sizing: border-box;
    border: 1px solid black;
    border-radius: 4px;
  }

  label {
    display: block;
    margin-bottom: 6px;
  }

  .error,
  .success {
    text-align: center;
    margin-top: 10px;
  }

  .error {
    color: red;
    font-size: 12px;
    display: block;
  }

  .success {
    color: green;
    font-size: 12px;
    display: block;
  }

  .submit-button {
    background-color: #4caf50;
    color: white;
    padding: 8px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    margin-top: 10px; /* Add some spacing between the form and the button */
  }
</style>
