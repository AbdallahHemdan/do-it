<template>
  <div class="home">
    <section class="todoapp">
      <header class="header">
        <h1>todo me</h1>
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
          @click="toggleAllTasks"
        />
        <label for="toggle-all">Mark all as complete</label>
        <ul class="todo-list">
          <li
            :class="{ todo: true, completed: todo.completed }"
            v-for="(todo, index) in filteredTasks"
            :key="index"
          >
            <div class="view">
              <input class="toggle" type="checkbox" v-model="todo.completed" />
              <label>{{ todo.title }}</label>
              <button class="destroy" @click="removeTask(index)"></button>
            </div>
            <input class="edit" type="text" />
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
export default {
  name: "Home",
  data: function() {
    return {
      newTodo: "",
      todos: [],
      visibility: "all"
    };
  },
  methods: {
    removeTask: function(indexOfTaskToRemove) {
      this.todos.splice(indexOfTaskToRemove, 1);
    },
    toggleAllTasks: function() {
      for (
        let indexOfTodo = 0;
        indexOfTodo < this.todos.length;
        indexOfTodo++
      ) {
        this.todos[indexOfTodo].completed ^= 1;
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
    }
  },
  computed: {
    leftTasks: function() {
      return filters.active(this.todos).length;
      return itemsLeft;
    },
    filteredTasks: function() {
      return filters[this.visibility](this.todos);
    },
    itemText: function() {
      return filters.active(this.todos).length > 1 ? "items" : "item";
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
</style>