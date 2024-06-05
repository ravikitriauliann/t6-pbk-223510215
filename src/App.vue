<template>
  <div class="container">
    <div class="table-wrapper">
      <table class="table table-dark table-striped">
        <thead>
          <tr>
            <th class="text-left">NO</th>
            <th class="text-left">Title</th>
            <th class="text-left">Content</th>
            <th class="text-right">Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(article, index) in articles" :key="article.id">
            <td class="text-left">{{ index + 1 }}</td>
            <td class="text-left">{{ article.title }}</td>
            <td class="text-left">{{ article.content }}</td>
            <td class="text-right">
              <button class="btn btn-secondary" @click="edit(article)">
                Edit
              </button>
              <button class="btn btn-danger" @click="remove(article.id)">
                Delete
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="form-wrapper">
      <form @submit.prevent="save" class="form-container">
        <input
          type="text"
          v-model="form.title"
          placeholder="Title"
          class="form-control"
        /><br />
        <textarea
          v-model="form.content"
          placeholder="Content"
          class="form-control"
        ></textarea
        ><br />
        <button type="submit" class="btn btn-success">Save Article</button>
      </form>
      <button @click="load" class="btn btn-primary">Load Articles</button>
      <p class="text-h6">Welcome to Data Buku View</p>
    </div>
  </div>
</template>

<script>
import { ref, reactive, onMounted } from "vue";
import axios from "axios";

export default {
  setup() {
    const form = reactive({
      id: null,
      title: "",
      content: "",
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get("http://localhost:3000/articles");
        articles.value = response.data;
      } catch (error) {
        console.error("Error loading articles:", error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `http://localhost:3000/articles/${form.id}`
          : "http://localhost:3000/articles";
        const method = form.id ? "put" : "post";
        const response = await axios[method](url, {
          title: form.title,
          content: form.content,
        });

        if (form.id) {
          articles.value = articles.value.map((article) =>
            article.id === response.data.id ? response.data : article
          );
        } else {
          articles.value.push(response.data);
        }

        form.id = null;
        form.title = "";
        form.content = "";
      } catch (error) {
        console.error("Error saving article:", error);
      }
    }

    async function remove(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter((article) => article.id !== id);
        console.log(`Article with id ${id} deleted successfully`);
      } catch (error) {
        console.error("Error deleting article:", error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, remove, load };
  },
};
</script>

<style>
body {
  background-color: #f4f4f9;
  font-family: Arial, sans-serif;
}

.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
}

.table-wrapper {
  width: 80%;
  margin-bottom: 30px;
}

.table {
  width: 100%;
  margin: auto;
}

.table th,
.table td {
  padding: 15px;
  border: 1px solid #212529;
}

.table th {
  background-color: #343a40;
  color: white;
}

.table-striped tbody tr:nth-of-type(odd) {
  background-color: rgba(0, 0, 0, 0.05);
}

.form-wrapper {
  width: 80%;
  max-width: 600px;
  margin: auto;
  background: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.form-container {
  display: flex;
  flex-direction: column;
}

.form-container input,
.form-container textarea {
  width: 100%;
  margin-bottom: 10px;
  padding: 10px;
  border: 1px solid #ced4da;
  border-radius: 4px;
}

.form-container button {
  padding: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.form-container button:hover {
  background-color: #0056b3;
}

.btn {
  padding: 10px;
  border-radius: 4px;
  cursor: pointer;
  color: white;
  border: none;
}

.btn:hover {
  opacity: 0.9;
}

.btn-success {
  background-color: #28a745;
  margin-top: 10px;
}

.btn-primary {
  background-color: #17a2b8;
  margin-top: 10px;
}

.btn-secondary {
  background-color: #ffc107;
}

.btn-danger {
  background-color: #dc3545;
}

.text-h6 {
  margin-top: 20px;
  font-size: 1.25rem;
  text-align: center;
}
</style>
