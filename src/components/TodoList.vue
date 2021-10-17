<template>
  <div>Todo List</div>
  <label v-for="label in options" v-bind:key="label.label">
    <input type="radio" v-model="current" v-bind:value="label.value" />{{
      label.label
    }}
  </label>
  {{ computedTodos.length }} 件を表示中
  <table>
    <thead>
      <tr>
        <th class="id">ID</th>
        <th class="comment">コメント</th>
        <th class="state">状態</th>
        <th class="button">-</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="item in computedTodos" v-bind:key="item.id">
        <th>{{ item.id }}</th>
        <td>{{ item.comment }}</td>
        <td class="state">
          <button v-on:click="doChangeState(item)">
            {{ labels[item.state] }}
          </button>
        </td>
        <td class="button">
          <button v-on:click="doRemove(item)">削除</button>
        </td>
      </tr>
    </tbody>
  </table>

  <h2>新しい作業の追加</h2>
  <form class="add-form" v-on:submit.prevent="doAdd">
    コメント <input type="text" ref="comment" />
    <button type="submit">追加</button>
  </form>
  <button v-on:click="doRemoveAll()">全てのタスクを削除</button>
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";
type Storage = {
  uid?: number | undefined;
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
    console.log(todos.length, "length");

    todoStorage.uid = todos.length;
    return todos;
  },
  save: function (todos) {
    localStorage.setItem(STORAGE_KEY, JSON.stringify(todos));
  },
};

// Todoデータの例
// [
//   { "id": 1, "comment": "新しいToDo1", "state": 0 },
//   { "id": 2, "comment": "新しいToDo2", "state": 0 }
// ]

type TodoItem = {
  id: number;
  comment: string;
  state: number;
};

@Options({
  data() {
    return {
      todos: [],
      options: [
        { value: -1, label: "すべて" },
        { value: 0, label: "作業中" },
        { value: 1, label: "完了" },
      ],
      current: -1,
    };
  },
  methods: {
    doAdd(event: any, value: string) {
      const comment = this.$refs.comment;
      if (!comment.value.length) {
        return;
      }

      this.todos.push({
        id: todoStorage.uid ? todoStorage.uid++ : 0,
        comment: comment.value,
        state: 0,
      });
      comment.value = "";
    },
    doChangeState(item: TodoItem) {
      item.state = item.state ? 0 : 1;
    },
    doRemove(item: TodoItem) {
      const index = this.todos.indexOf(item);
      this.todos.splice(index, 1);
    },
    doRemoveAll() {
      this.todos = [];
    },
  },
  watch: {
    todos: {
      handler: function (todos) {
        todoStorage.save(todos);
      },
      deep: true,
    },
  },
  created() {
    this.todos = todoStorage.fetch();
  },
  computed: {
    computedTodos: function () {
      return this.todos.filter(function (this: any, el: TodoItem) {
        return this.current < 0 ? true : this.current === el.state;
      }, this);
    },
    labels() {
      return this.options.reduce(function (a: any, b: any) {
        return Object.assign(a, { [b.value]: b.label });
      }, {});
      // キーから見つけやすいように、次のように加工したデータを作成
      // {0: '作業中', 1: '完了', -1: 'すべて'}
    },
  },
})
export default class TodoList extends Vue {}
</script>

<style scoped>
* {
  box-sizing: border-box;
}
#app {
  max-width: 640px;
  margin: 0 auto;
}
table {
  width: 100%;
  border-collapse: collapse;
}
thead th {
  border-bottom: 2px solid #0099e4; /*#d31c4a  */
  color: #0099e4;
}
th,
th {
  padding: 0 8px;
  line-height: 40px;
}
thead th.id {
  width: 50px;
}
thead th.state {
  width: 100px;
}
thead th.button {
  width: 60px;
}
tbody td.button,
tbody td.state {
  text-align: center;
}
tbody tr td,
tbody tr th {
  border-bottom: 1px solid #ccc;
  transition: all 0.4s;
}
tbody tr.done td,
tbody tr.done th {
  background: #f8f8f8;
  color: #bbb;
}
tbody tr:hover td,
tbody tr:hover th {
  background: #f4fbff;
}
button {
  border: none;
  border-radius: 20px;
  line-height: 24px;
  padding: 0 8px;
  background: #0099e4;
  color: #fff;
  cursor: pointer;
}
</style>
