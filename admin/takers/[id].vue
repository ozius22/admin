<template>
  <div>
    <div class="actions">
      <button class="action-btn view" @click="back()">Back</button>
    </div>
    <div v-if="data">
      <div v-for="taker in data" :key="taker.id" class="taker-details">
        <p><strong>First Name:</strong> {{ taker.first_name }}</p>
        <p><strong>Last Name:</strong> {{ taker.last_name }}</p>
        <p><strong>Age:</strong> {{ taker.age }}</p>
        <p><strong>Email:</strong> {{ taker.email }}</p>
      </div>
    </div>
    <div v-else>
      <p>No taker details available.</p>
    </div>
  </div>
</template>

<script>
export default {
  setup() {
    try {
      definePageMeta({
          layout: 'admin-layout',
        });

      const { id } = useRoute().params;
      const uri = 'http://127.0.0.1:8000/api/takers/' + id;

      const fetchData = async () => {
        try {
          const response = await fetch(uri);

          if (response.ok) {
            const data = await response.json();
            return data;
          } else {
            console.error('Error fetching data:', response.statusText);
            return null;
          }
        } catch (error) {
          console.error('Error fetching data:', error);
          return null;
        }
      };

      const data = ref(null);

      fetchData().then((result) => {
        data.value = result;
      });

      return { data };
    } catch (error) {
      console.error('Error fetching data:', error);
    }
  },

  methods: {
    back() {
      this.$router.go(-1);
    },
  },
};
</script>


<style scoped>
.taker-details {
  margin-top: 20px;
}
</style>