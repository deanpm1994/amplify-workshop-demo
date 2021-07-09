<template>
  <amplify-authenticator>
    <div id="app">
      <h1>Todo App</h1>
      <input type="text" v-model="name" placeholder="Todo name" />
      <input type="text" v-model="description" placeholder="Todo description" />
      <button v-on:click="createTodo">Create Todo</button>
    </div>

    <Todos :todos="todos" />

    <amplify-sign-out></amplify-sign-out>
  </amplify-authenticator>
</template>

<script>
import { API, Auth } from 'aws-amplify';
import { createTodo } from './graphql/mutations';
import { listTodos } from './graphql/queries';
import { onCreateTodo } from './graphql/subscriptions';
import Todos from './components/Todos.vue'

export default {
  name: 'App',
  components: {
    Todos
  },
  async created() {
    this.getTodos();
    this.subscribe();
  },
  data() {
    return {
      name: '',
      description: '',
      todos: []
    }
  },
  methods: {
    async createTodo() {
      const { name, description } = this;
      if (!name || !description) return;

      try {
        const { attributes: { email }} = await Auth.currentAuthenticatedUser()

        const todo = { name, description, authorEmail: email };
        this.todos = [...this.todos, todo];
        await API.graphql({
          query: createTodo,
          variables: {input: todo},
        });
        this.name = '';
        this.description = '';
      } catch {
        //
      }
    },
    async getTodos() {
      const todos = await API.graphql({
        query: listTodos
      });
      this.todos = todos.data.listTodos.items;
    },
    subscribe() {
      API.graphql({ query: onCreateTodo })
        .subscribe({
          next: (eventData) => {
            let todo = eventData.value.data.onCreateTodo;
            if (this.todos.some(item => item.name === todo.name)) return; // remove duplications
            this.todos = [...this.todos, todo];
          }
        });
    },
  }
}
</script>

<style>
body {
  margin: 0;
}
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
