<template>
  <div class="todo-app">
    <div class="todo-container">
      <h1 class="todo-title">To Do List</h1>
      <div class="input-group">
        <input v-model="newTodo.text" @keyup.enter="addTodo" placeholder="Add new todo" class="todo-input">
        <button @click="addTodo" class="btn add-btn">Add</button>
      </div>
      <div class="input-group">
        <select v-model="newTodo.priority" class="todo-priority">
          <option value="Low">Low</option>
          <option value="Medium">Medium</option>
          <option value="High">High</option>
        </select>
        <input type="datetime-local" v-model="newTodo.deadline" class="todo-deadline">
      </div>
      <input v-model="searchKeyword" @input="searchTodo" placeholder="Search todo" class="todo-search">
      <div class="filter-buttons">
        <button @click="filterByStatus('all')" :class="{ 'active': statusFilter === 'all' }">All</button>
        <button @click="filterByStatus('active')" :class="{ 'active': statusFilter === 'active' }">Active</button>
        <button @click="filterByStatus('completed')" :class="{ 'active': statusFilter === 'completed' }">Completed</button>
      </div>
      <table class="todo-table">
        <thead>
          <tr>
            <th>Task</th>
            <th>Priority</th>
            <th>Deadline</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(todo, index) in filteredTodos" :key="index" :class="{ 'completed': todo.completed }">
            <td @click="toggleCompleted(index)" :class="{ 'completed-text': todo.completed }">{{ todo.text }}</td>
            <td class="priority-cell">{{ todo.priority }}</td>
            <td class="deadline-cell">{{ todo.deadline }}</td>
            <td class="action-cell">
              <button @click="removeTodo(index)" class="btn delete-btn">Delete</button>
              <button @click="editTodo(index)" class="btn edit-btn">Edit</button>
              <input type="checkbox" v-model="todo.completed" class="todo-checkbox">
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      newTodo: {
        text: '',
        priority: 'Low',
        deadline: '',
        completed: false
      },
      todos: [],
      searchKeyword: '',
      statusFilter: 'all'
    };
  },
  computed: {
    filteredTodos() {
      return this.todos.filter(todo =>
        todo.text.toLowerCase().includes(this.searchKeyword.toLowerCase()) &&
        (this.statusFilter === 'all' || (this.statusFilter === 'active' && !todo.completed) || (this.statusFilter === 'completed' && todo.completed))
      );
    }
  },
  methods: {
    addTodo() {
      if (this.newTodo.text.trim() !== '') {
        this.todos.push({ 
          text: this.newTodo.text, 
          priority: this.newTodo.priority, 
          deadline: this.newTodo.deadline,
          completed: false 
        });
        this.saveToLocalStorage();
        this.resetNewTodo();
      }
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
      this.saveToLocalStorage();
    },
    editTodo(index) {
      const editedTodo = this.todos[index];
      this.newTodo = {
        text: editedTodo.text,
        priority: editedTodo.priority,
        deadline: editedTodo.deadline
      };
      this.removeTodo(index);
    },
    resetNewTodo() {
      this.newTodo = {
        text: '',
        priority: 'Low',
        deadline: '',
        completed: false
      };
    },
    searchTodo() {
      // Search functionality implemented in computed property.
    },
    toggleCompleted(index) {
      this.todos[index].completed = !this.todos[index].completed;
      this.saveToLocalStorage();
    },
    saveToLocalStorage() {
      localStorage.setItem('todos', JSON.stringify(this.todos));
    },
    loadFromLocalStorage() {
      const todos = localStorage.getItem('todos');
      if (todos) {
        this.todos = JSON.parse(todos);
      }
    },
    filterByStatus(status) {
      this.statusFilter = status;
    }
  },
  created() {
    this.loadFromLocalStorage();
  }
};
</script>

<style scoped>
.todo-app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: linear-gradient(45deg, #FFC107, #FF5252, #8BC34A, #03A9F4, #9C27B0);
  background-size: 1000% 1000%;
  animation: rainbowBG 10s ease infinite;
}

@keyframes rainbowBG {
  0% {
    background-position: 0% 50%;
  }
  50% {
    background-position: 100% 50%;
  }
  100% {
    background-position: 0% 50%;
  }
}

.todo-container {
  max-width: 800px;
  width: 100%;
  padding: 20px;
  border-radius: 10px;
  background-color: #fff;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
}

.todo-title {
  text-align: center;
  margin-bottom: 20px;
  color: #333;
}

.input-group {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

.todo-input,
.todo-priority,
.todo-deadline,
.todo-search {
  flex: 1;
  padding: 10px;
  margin-right: 10px;
  border: 1px solid #f1b2b2;
  border-radius: 5px;
  color: #f5f1f1;
}

.todo-input:focus,
.todo-priority:focus,
.todo-deadline:focus,
.todo-search:focus {
  outline: none;
  border-color: #007bff;
}

.filter-buttons {
  margin-bottom: 10px;
}

.filter-buttons button {
  margin-right: 5px;
  padding: 5px 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  background-color: transparent;
  color: #034124;
}

.filter-buttons button.active {
  background-color: #02605d;
  color: #fff;
}

.todo-table {
  width: 100%;
  border-collapse: collapse;
}

.todo-table th,
.todo-table td {
  padding: 8px;
  text-align: left;
  border-bottom: 1px solid #0c7d42;
  color: #333;
}

.todo-table th {
  background-color: #17e3f2;
}

.todo-table .priority-cell {
  width: 100px;
}

.todo-table .deadline-cell {
  width: 150px;
}

.todo-table .action-cell {
  width: 150px;
}

.todo-table .completed-text {
  text-decoration: line-through;
  opacity: 0.7;
}

.todo-checkbox {
  margin-left: 10px;
}

.btn {
  padding: 5px 10px;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  color: #f6f8f7;
}

.add-btn {
  background-color: #067d22;
}

.delete-btn {
  background-color: #dc3545;
}

.edit-btn {
  background-color: #1f5f70;
}

.btn:hover {
  filter: brightness(90%);
}

.completed {
  background-color: #c5f8f0;
}
</style>
