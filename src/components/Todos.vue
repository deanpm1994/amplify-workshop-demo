<template>
  <div id="todos">
    <div class="todo" v-for="todo in todos" :key="todo.id">
      <h3>{{ todo.name }}</h3>
      <p>{{ todo.description }}</p>
      <small>{{ todo.authorEmail }}</small>
      <div class="actions">
        <button type="button" class="btn btn-danger" v-on:click="deleteTodo(todo.id)">Delete</button>
      </div>
    </div>
  </div>
</template>

<script>
import { API, graphqlOperation } from 'aws-amplify';
import * as mutations from '../graphql/mutations';

export default {
  name: 'Todo',
  props: ['todos'],
  methods: {
    async deleteTodo(id) {
      await API.graphql(graphqlOperation(mutations.deleteTodo, { input: { id }}))
    }
  }
}
</script>

<style>
  #todos {
    width: 100%;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
    align-items: flex-start;
  }
  .todo {
    width: 30%;
    padding: 5px;
    margin-top: 5px;
    margin-bottom: 10px;
    border-radius: 10px;
    box-shadow: 0 .5rem 1rem rgba(0,0,0,.15);
  }
  .todo .actions {
    padding: 10px 5px;
    display: flex;
    justify-content: flex-end;
  }
  .todo .actions button.btn.btn-danger {
    background: rgb(235, 12, 12);
    color: white;
    border-radius: 5px;
    border: 1px solid #ccc;
    font-size: 14px;
    font-weight: 500;
    text-transform: uppercase;
    cursor: pointer;
    padding: 5px 10px;
  }
  .todo .actions button.btn.btn-danger:hover {
    background: rgb(236, 75, 75);
  }
</style>
