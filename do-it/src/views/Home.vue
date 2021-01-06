<template>
  <div class="home">
    <section class="todoapp">
      <header class="header">
        <h1>do it</h1>
        <input
          class="new-todo"
          autocomplete="off"
          placeholder="What needs to be done?"
          v-model="newTodo"
          @keyup.enter="addNewTask"
        />
      </header>
      <section class="main">
        <input
          class="toggle-all"
          id="toggle-all"
          type="checkbox"
          v-model="allIsDone"
        />
        <label for="toggle-all">Mark all as complete</label>
        <ul class="todo-list">
          <li
            :class="{
              todo: true,
              completed: todo.completed,
              editing: todo === editingTodo
            }"
            v-for="(todo, index) in filteredTasks"
            :key="index"
          >
            <div class="view">
              <input class="toggle" type="checkbox" v-model="todo.completed" />
              <label @dblclick="editTodo(todo)">{{ todo.title }}</label>
              <button class="destroy" @click="removeTask(index)"></button>
            </div>
            <input
              class="edit"
              type="text"
              v-model="todo.title"
              @keyup.enter="editingIsDone"
              @keyup.esc="cancleEditing"
            />
          </li>
        </ul>
      </section>
      <footer class="footer">
        <span class="todo-count">
          <strong>{{ leftTasks }}</strong> {{ itemText }} left
        </span>
        <ul class="filters">
          <li>
            <a
              @click.prevent="filterTasks('all')"
              :class="{ selected: visibility === 'all' }"
              >All</a
            >
          </li>
          <li>
            <a
              @click.prevent="filterTasks('active')"
              :class="{ selected: visibility === 'active' }"
              >Active</a
            >
          </li>
          <li>
            <a
              @click.prevent="filterTasks('completed')"
              :class="{ selected: visibility === 'completed' }"
              >Completed</a
            >
          </li>
        </ul>
        <button class="clear-completed" @click="clearCompleted">
          Clear completed
        </button>
      </footer>
    </section>
    <footer class="info">
      <h3 class="instruction">Double-click to edit a todo</h3>
    </footer>
  </div>
</template>

<script>
let filters = {
  all: function(todos) {
    return todos;
  },
  active: function(todos) {
    return todos.filter(todo => !todo.completed);
  },
  completed: function(todos) {
    return todos.filter(todo => todo.completed);
  }
};

let todosLocalStorge = {
  fetchTodos: function() {
    return JSON.parse(localStorage.getItem("todos")) || [];
  },
  saveTodos: function(todos) {
    localStorage.setItem("todos", JSON.stringify(todos));
  }
};

export default {
  name: "Home",
  data: function() {
    return {
      newTodo: "",
      todos: todosLocalStorge.fetchTodos(),
      visibility: "all",
      editingTodo: null,
      oldTaskDescription: null
    };
  },
  methods: {
    removeTask: function(indexOfTaskToRemove) {
      this.todos.splice(indexOfTaskToRemove, 1);
    },
    toggleAllTasks: function() {
      let isActiveExists = !!filters.active(this.todos).length;
      for (
        let indexOfTodo = 0;
        indexOfTodo < this.todos.length;
        indexOfTodo++
      ) {
        this.todos[indexOfTodo].completed = isActiveExists;
      }
    },
    addNewTask: function() {
      if (this.newTodo.trim()) {
        this.todos.push({
          title: this.newTodo,
          completed: false
        });
        this.newTodo = "";
      }
    },
    filterTasks: function(typeOfTasks) {
      this.visibility = typeOfTasks;
    },
    clearCompleted: function() {
      this.todos = this.todos.filter(todo => !todo.completed);
    },
    editTodo: function(todo) {
      this.editingTodo = todo;
      this.oldTaskDescription = todo.title;
    },
    editingIsDone: function() {
      if (!this.editingTodo.title) {
        this.removeTask(this.todos.indexOf(this.editingTodo));
      }
      this.editingTodo = null;
    },
    cancleEditing: function() {
      this.editingTodo.title = this.oldTaskDescription;
      this.editingTodo = null;
    }
  },
  computed: {
    leftTasks: function() {
      return filters.active(this.todos).length;
    },
    filteredTasks: function() {
      return filters[this.visibility](this.todos);
    },
    itemText: function() {
      return filters.active(this.todos).length > 1 ? "items" : "item";
    },
    allIsDone: {
      get: function() {
        return this.leftTasks === 0;
      },
      set: function(value) {
        this.todos.forEach(todo => {
          return (todo.completed = value);
        });
      }
    }
  },
  watch: {
    todos: {
      handler: function(todos) {
        todosLocalStorge.saveTodos(this.todos);
      },
      deep: true
    }
  }
};
</script>


<style scoped>
.instruction {
  font-size: 20px;
}

.filters li {
  cursor: pointer;
}
.todo-icon {
  width: 50px;
}
</style>
