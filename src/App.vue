<template>
  <div>
    <input v-model="searchQuery" @input="this.searchTodos(this.searchQuery)" placeholder="name"/>
    <ul v-if="matchingTodos.length > 0">
      <li v-for="todo in matchingTodos" :key="todo.id" @click="selectTodo(todo.id)">
        {{ todo.title }}
      </li>
    </ul>
    <div v-if="selectedTodo">
      {{selectedTodo.title}}
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import _ from 'lodash';

export default {
  name: 'App',
  data() {
    return {
      todos: [],
      searchQuery: '',
      matchingTodos: [],
      selectedTodo: {},
    };
  },
  mounted() {
    axios.get(`https://jsonplaceholder.typicode.com/todos`).then(response => {
      this.todos = response.data;
    });
  },
  methods: {
    searchTodos: _.debounce(function () {
      this.matchingTodos = this.todos.filter(todo =>
          todo.title.toLowerCase().includes(this.searchQuery.toLowerCase())
      ).slice(0, 5);
    }, 300),
    async selectTodo(todoId) {
      try {
        const response = await axios.get(`https://jsonplaceholder.typicode.com/todos/${todoId}`);
        this.selectedTodo = response.data;
        this.searchQuery = "";
        this.matchingTodos = [];
      } catch (e) {
        console.log(e);
      }
    }
  }
}
</script>

<style>
div {
  padding: 10px;
}
ul {
  display: block;
  list-style: none;
  margin-block-start: 0px;
  margin-block-end: 0px;
  margin-inline-start: 0px;
  margin-inline-end: 0px;
  padding-inline-start: 0px;
  margin-top: 5px;
}
li:hover {
  cursor: pointer;
}
</style>
