<template>
  <form @submit.prevent="submitForm">
    <div>
      <div class="actions">
        <button class="action-btn view" @click="back()">Back</button>
      </div>
      <div v-if="data">
        <div v-for="j in data" :key="j.id" class="admin-details">
          <label for="firstName">First Name:</label>
          <input v-model="j.question" id="first_name" type="text" class="form-field">

          <button type="submit" class="submit-button">Save Changes</button>
        </div>
      </div>
      <div v-else>
        <p>No admin Question available.</p>
      </div>
    </div>
  </form>
</template>

<script>
export default {
  setup() {
    try {
      const { id } = useRoute().params;
      const uri = 'http://127.0.0.1:8000/api/questions/' + id;

      const { data } = useFetch(uri);

      definePageMeta({
        layout: 'admin-layout',
      });

      const formData = ref({
        question: '',
      });

      const formErrors = ref({});
      const successMessage = ref('');

      const submitForm = async () => {
      try {
        const response = await fetch('http://127.0.0.1:8000/api/admins/' + adminID + '/edit', {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(formData.value),
        });

        if (response.ok) {
          formErrors.value = {};
          successMessage.value = 'Admin edited successfully!';
        } else {
          const responseData = await response.json();
          if (responseData && responseData.errors) {
            formErrors.value = responseData.errors;
            console.error('Validation errors:', responseData.errors);
          } else {
            console.error('Unexpected error format:', responseData);
          }
        }
      } catch (error) {
        console.error('Error submitting form:', error);
      }
    };


      return { data, formData, formErrors, successMessage, submitForm };
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  },

  methods: {
    back() {
      this.$router.push('/admin/questions');
    },
  }
};
</script>


<style scoped>
  .admin-details {
    margin-bottom: 20px;
  }

  .form-field {
    width: calc(100% - 12px);
    padding: 8px;
    margin-bottom: 10px;
    box-sizing: border-box;
    border: 1px solid black;
    border-radius: 4px;
  }

  .submit-button {
    background-color: #4caf50;
    color: white;
    padding: 8px;
    border: none;
    border-radius: 4px;
    cursor: pointer;
    margin-top: 10px;
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
</style>