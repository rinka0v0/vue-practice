<template>
  <div>Todo List</div>
  <div>hello!!!</div>
</template>

<script lang="ts">
import { Vue } from "vue-class-component";
type Storage = {
  uid: number | undefined;
  fetch: () => any;
  save: (todos: any) => void;
};
const STORAGE_KEY = "todos-vuejs-demo";
const todoStorage: Storage = {
  fetch: function () {
    const todos = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
    todos.forEach(function (todo: any, index: any) {
      todo.id = index;
    });
    todoStorage.uid = todos.length;
    return todos;
  },
  save: function (todos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos));
  },
  uid: undefined,
};

// type TodoType = {
//   id: number;
//   comment: string;
//   state: number;
// };

// @Options({
//   props: {
//     todos: [],
//   },
// })
export default class TodoList extends Vue {}
</script>
