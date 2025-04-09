<template>
  <div class="container">
      <div class="header">
          <h1>TodoList</h1>
          <form @submit.prevent="addTodo">
              <input v-model="newTodo" class="input" type="text" placeholder="Add task">
              <button class="btn" type="submit">Add</button>
          </form>
      </div>

      <transition-group name="fade" tag="ul" class="tasks">
          <TodoItem
              v-for="todo in todos"
              :key="todo.id"
              :todo="todo"
              @updateTodo="saveTodos"
              @deleteTodo="deleteTodo"
              @showAlert="showAlertMethod"
          />
      </transition-group>
      <div class="alert" :class="{ show: showAlert }">Task can't be empty</div>
  </div>
</template>

<script>
import TodoItem from './components/TodoItem.vue';

export default {
  components: { TodoItem },
  data() {
      return {
          todos: [],
          newTodo: "",
          showAlert: false
      };
  },
  methods: {
      async addTodo() {
          const todoTitle = this.newTodo.trim();
          if (todoTitle.length > 0) {
              const newId = this.todos.length > 0 
                  ? Math.max(...this.todos.map(t => t.id)) + 1 
                  : 1;
              this.todos.push({ id: newId, title: todoTitle, done: false });
              this.newTodo = "";
              await this.saveTodos();
          } else {
              this.showAlertMethod();
          }
      },
      async deleteTodo(id) {
          this.todos = this.todos.filter(todo => todo.id !== id);
          await this.saveTodos();
      },
      showAlertMethod() {
          this.showAlert = true;
          setTimeout(() => (this.showAlert = false), 3000);
      },
      async saveTodos() {
          try {
              await new Promise(resolve => setTimeout(resolve, 500)); // Имитация API
              localStorage.setItem("todos", JSON.stringify(this.todos));
          } catch (error) {
              console.error("Ошибка сохранения:", error);
          }
      },
      async loadTodos() {
          const storedTodos = localStorage.getItem("todos");
          if (storedTodos) {
              this.todos = JSON.parse(storedTodos);
              return;
          }
          try {
              const response = await fetch("/tasks.json");
              const data = await response.json();
              this.todos = data;
          } catch (error) {
              console.error("Ошибка загрузки tasks.json:", error);
              this.todos = [];
          }
      }
  },
  async mounted() {
      await this.loadTodos();
  }
};
</script>

<style scoped>
* {
  margin: 0;
  padding: 0;
  background-color: #000000;
  color: #FFFFFF;
  overflow-x: hidden;
}

.container {
  margin-top: 20px;
  display: flex;
  height: 100%;
  width: 100%;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  padding: 10px;
}

.input {
  margin-top: 20px;
  padding: 11px;
  color: #86868B;
  background-color: #1C1C1E;
  border-radius: 8px;
  border: none;
  transition: all 300ms;
}

.input:hover {
  background-color: #2C2C2E;
  color: #FFFFFF;
}

ul {
  margin-top: 20px;
  padding: 10px;
  list-style-type: none;
}

.btn {
  background-color: #007AFF;
  color: #FFFFFF;
  border: none;
  padding: 10px 20px;
  border-radius: 8px;
  font-size: 14px;
  font-weight: 500;
  cursor: pointer;
  transition: all 200ms ease;
}

.btn:hover {
  background-color: #0066CC;
  transform: scale(1.02);
}

.btn:active {
  transform: scale(0.98);
}

.alert {
  background-color: rgba(255, 255, 255, 0.9);
  color: #000000;
  padding: 15px 20px;
  border-radius: 12px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1000;
  transition: opacity 0.5s ease;
  opacity: 0;
  pointer-events: none;
}

.alert.show {
  opacity: 1;
  pointer-events: auto;
}

.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}

.fade-enter, .fade-leave-to {
  opacity: 0;
}

@media (max-width: 640px) {
  .container {
      padding: 5px;
  }
  form {
      display: flex;
      flex-direction: column;
      align-items: stretch;
      gap: 10px;
  }
  .input, .btn {
      width: 100%;
  }
}
</style>