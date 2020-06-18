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
      todos: []
    };
  },
  methods: {
    addNewTask: function() {
      if (this.newTodo.trim()) {
        this.todos.push({
          title: this.newTodo,
          completed: false
        });
        this.newTodo = "";
      }
    }
  }
};
</script>


<style scoped>
.instruction {
  font-size: 20px;
}
</style>