<template>
  <!-- <div>todo list</div> -->
  <TodoList />
</template>

<script lang="ts">
import { Vue, Options } from "vue-class-component";
import TodoList from "@/components/TodoList.vue";
@Options({
  components: {
    TodoList,
  },
})
export default class Todo extends Vue {}
</script>
