<template>
  <div>
    <p>Questions List</p>
    <button class="action-btn create" @click="create()">Create</button>
    <table>
      <thead>
        <tr>
          <th>Question</th>
          <th>Admin</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="t in data.questions" :key="t.id">
          <td>
            <nuxt-link :to="'/questions/' + t.id">{{ `${t.question}` }}</nuxt-link>
          </td>
          <td>
              <nuxt-link :to="'/admins/' + t.id">{{ `${t.adminID}` }}</nuxt-link>
          </td>
          <td>
            <button class="action-btn view" @click="view(t.id)">View</button>
            <button class="action-btn edit" @click="edit(t.id)">Edit</button>
            <button class="action-btn delete" @click="deleteQuestion(t.id)">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  async setup() {
    try {
      const { data } = await useFetch('http://127.0.0.1:8000/api/questions/');

      definePageMeta({
        layout: 'admin-layout',
      });

      return { data };
    } catch (error) {
      console.error('Error fetching:', error);
      // You might want to handle errors more gracefully, for example by showing an error message to the user
    }
  },

  methods: {
    view(id) {
      this.$router.push(`/admin/questions/${id}`);
    },

    edit(adminId) {
      this.$router.push(`/admin/questions/edit/${adminId}`);
    },

    create() {
      this.$router.push(`/admin/questions/create`);
    },

    async deleteQuestion(id) {
      try {
        const response = await fetch(`http://127.0.0.1:8000/api/questions/${id}/1/delete`, {
          method: 'DELETE',
        });

        if (response.ok) {
          window.location.reload();
        } else {
          console.error('Failed to delete question:', response.statusText);
        }
      } catch (error) {
        console.error('Error deleting question:', error);
      }
    },
  },
};
</script>

<style>
/* Add your table styles here if needed */
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

th, td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

th {
  background-color: #f2f2f2;
}

.action-btn {
  margin-right: 5px;
  padding: 3px 8px;
  cursor: pointer;
  border: none;
  border-radius: 4px;
}

.view {
  background-color: green;
  color: white;
}

.edit {
  background-color: yellow;
  color: #333;
}

.delete {
  background-color: red;
  color: white;
}
</style>