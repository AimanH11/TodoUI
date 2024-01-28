<template>
    <div class="todo-list-container">
    <h1 class="text-2xl font-bold mb-3">Todo List</h1>
    <div class="flex mb-4">
      <div class="header justify">
      <button @click="showAddForm">Add Todo</button>
    </div>
    </div>
    <form v-if="showForm" @submit.prevent="addItem" class="add-form">
      <label for="newItem">New Todo:</label>
      <div class="input-group">
        <input type="text" v-model="newItem.title" placeholder="Title" required />
        <input type="text" v-model="newItem.description" placeholder="Description" />
        <button type="submit">Add</button>
      </div>
    </form>
    <div v-if="todoItems.length > 0">
      <h2>Tasks:</h2>
      <table class="todo-table">
        <thead>
          <tr class="todo-item">
            <th>Title</th>
            <th>Description</th>
            <th>Action</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in todoItems" :key="item.id" class="todo-item">
            <td :class="{ completed: item.completed }">{{ item.title }}</td>
            <td :class="{ completed: item.completed }">{{ item.description }}</td>
            <td>
              <button class="todo-complete" v-if="!item.completed" @click="completeItem(item.id)">Complete</button>
              <span v-else>Completed</span>
              <button  class="delete-button" @click="deleteItem(item)">Delete</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
   </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      todoItems: [],
      newItem: {
        title: '',
        description: '',
        completed: false // Default value for new todo
      },
      showForm: false
    };
  },
  mounted() {
    this.fetchTodos();
  },
  methods: {
    fetchTodos() {
      axios.get('http://localhost:8000/api/todos')
        .then(response => {
          this.todoItems = response.data;
        })
        .catch(error => {
          console.error('Axios error:', error.response);
        });
    },
    addItem() {
      axios.post('http://localhost:8000/api/todos', this.newItem)
        .then(response => {
          this.todoItems.push(response.data);
          this.resetForm();
        });
    },
    deleteItem(item) {
      axios.delete(`http://localhost:8000/api/todos/${item.id}`)
        .then(() => {
          this.todoItems = this.todoItems.filter(todo => todo.id !== item.id);
        });
    },
    showAddForm() {
      this.showForm = true;
    },
    completeItem(item) {
      axios.put(`http://localhost:8000/api/todos/${item}`, { completed: true })
      .then(() => {
        this.fetchTodos();
      });
    },
    resetForm() {
      this.newItem = { title: '', description: '', completed: false };
      this.showForm = false;
    }
  }
};
</script>

<style scoped>
.todo-list-container {
  max-width: 600px;
  margin: auto;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.header button {
  cursor: pointer;
  padding: 8px;
  background-color: #4caf50;
  color: white;
  border: none;
}

.add-form {
  margin-bottom: 16px;
}

.input-group {
  display: flex;
}

.input-group input {
  flex: 1;
  padding: 8px;
}

.input-group button {
  cursor: pointer;
  padding: 8px;
  background-color: #4caf50;
  color: white;
  border: none;
}
.todo-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 16px;
}

.todo-table th, .todo-table td {
  border: none;
  padding: 12px;
  margin: 0;
  text-align: left;
  vertical-align: top;
}

.todo-table th {
  background-color: #f2f2f200;
  font-weight: bold;
}

.todo-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 8px;
}

.todo-item span {
  flex: 1;
}

.todo-item.completed span {
  text-decoration: line-through;
  color: #020202;
}

.todo-item button {
  cursor: pointer;
  padding: 8px; /* Adjust padding as needed */
  margin-left: 8px;
  border: none;
  color: white;
}

.todo-complete {
  background-color: #4caf50;
}

.delete-button {
  background-color: #f44336;
}

.completed-text {
  color: #000000;
}
</style>