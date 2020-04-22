<template>
  <div id="app" class="todoapp">
    <header class="header">
      <h1>todos</h1>
      <input
        class="new-todo"
        type="text"
        v-model="newTitle"
        autofocus
        placeholder="What needs to be done?"
        v-on:keyup.enter="addTodo()"
      />
    </header>
    <section class="main" v-show="filterTodos.length">
      <input class="toggle-all" type="checkbox" v-model="allDone" />
      <label for="toggle-all" v-on:click="toggleAllTodo()">Mark all as complete</label>
      <ul class="todo-list">
        <li
          class="todo"
          v-for="todo in filterTodos"
          v-bind:key="todo.id"
          v-bind:class="{completed: todo.completed, editing: todo === editTodo}"
        >
          <div class="view">
            <input
              class="toggle"
              type="checkbox"
              v-model="todo.completed"
              v-on:click="toggleTodo(todo)"
            />
            <label v-on:dblclick="selectTodo(todo)">{{ todo.title }}</label>
            <button class="destroy" v-on:click="removeTodo(todo)"></button>
          </div>
          <input
            class="edit"
            type="text"
            v-focus="todo === editTodo"
            v-model.trim.lazy="todo.title"
            v-on:blur="doneEdit(todo)"
            v-on:keyup.enter="doneEdit(todo)"
            v-on:keyup.esc="cancelEdit()"
          />
        </li>
      </ul>
    </section>
    <footer class="footer" v-show="todos.length">
      <span class="todo-count">
        <strong v-text="remaining"></strong>
        {{pluralize('item', remaining)}} left
      </span>
      <ul class="filters">
        <li>
          <a
            v-on:click="changeVisibility('all')"
            v-bind:class="{selected: visibility === 'all'}"
          >All</a>
        </li>
        <li>
          <a
            v-on:click="changeVisibility('active')"
            v-bind:class="{selected: visibility === 'active'}"
          >Active</a>
        </li>
        <li>
          <a
            v-on:click="changeVisibility('completed')"
            v-bind:class="{selected: visibility === 'completed'}"
          >Completed</a>
        </li>
      </ul>
      <button
        class="clear-completed"
        v-on:click="removeCompleted()"
        v-show="todos.length > remaining"
      >Clear completed</button>
    </footer>
  </div>
</template>

<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      newTitle: null,
      todos: [
        { id: 1, completed: true, title: "Foo" },
        { id: 2, completed: false, title: "Bar" },
        { id: 3, completed: true, title: "hoge" },
        { id: 4, completed: false, title: "fuga" }
      ],
      editTodo: null,
      visibility: "all"
    };
  },
  computed: {
    filterTodos() {
      switch (this.visibility) {
        case "all":
          return this.todos;
        case "active":
          return this.todos.filter(todo => !todo.completed);
        case "completed":
          return this.todos.filter(todo => todo.completed);
        default:
          return [];
      }
    },
    remaining() {
      return this.todos.filter(todo => !todo.completed).length;
    },
    allDone() {
      return (
        this.todos.length === this.todos.filter(todo => todo.completed).length
      );
    }
  },
  methods: {
    pluralize(word, count) {
      return word + (count === 1 ? "" : "s");
    },
    addTodo() {
      if (!this.newTitle) {
        return;
      }
      this.todos.push({
        id: this.todos.length + 1,
        completed: false,
        title: this.newTitle
      });
      this.newTitle = null;
    },
    removeTodo(todo) {
      const index = this.todos.indexOf(todo);
      this.todos.splice(index, 1);
    },
    removeCompleted() {
      this.todos = this.todos.filter(todo => !todo.completed);
    },
    toggleTodo(todo) {
      todo.completed = !todo.completed;
    },
    toggleAllTodo() {
      const val = this.allDone;
      this.todos.forEach(todo => (todo.completed = !val));
    },
    selectTodo(todo) {
      this.editTodo = todo;
    },
    doneEdit(todo) {
      if (!this.editTodo) {
        return;
      }
      if (!todo.title) {
        this.removeTodo(todo);
      }
      this.editTodo = null;
    },
    cancelEdit() {
      this.editTodo = null;
    },
    changeVisibility(mode) {
      this.visibility = mode;
    }
  },
  directives: {
    focus: function(el, binding) {
      if (binding) {
        el.focus();
      }
    }
  }
};
</script>

<style src="todomvc-app-css/index.css"></style>
