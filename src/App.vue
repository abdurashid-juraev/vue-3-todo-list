<script setup>
// @ts-nocheck

import { ref, watch, computed, onMounted, onUpdated } from "vue";

const inputField = ref("");
const todos = ref([]);
const show = ref(true);
const addBtn = () => {
  if (inputField.value !== "") {
    todos.value.push({
      inputField: inputField.value,
    });
  }

  inputField.value = "";
};
const removeBtn = (todo) => {
  todos.value = todos.value.filter((el) => el !== todo);
};
const clearTodos = () => {
  todos.value = [];
};
const todosLength = computed(() => {
  return todos.value.length;
});
onUpdated(() => {
  if (todos.value.length > 1) {
    show.value = false;
  }
  if (todos.value.length === null || todos.value.length < 2) {
    setTimeout(() => {
      show.value = true;
    }, 2000);
  }
}),
  watch(
    todos,
    (newValue) => {
      localStorage.setItem("todos", JSON.stringify(newValue));
    },
    {
      deep: true,
    }
  );

onMounted(() => {
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main class="wrap">
    <div>
      <h1>todo app</h1>

      <div>
        <form @submit.prevent="addBtn" class="flex">
          <input
            class="input-field"
            type="text"
            v-model="inputField"
            placeholder="Add your new todo"
          />
          <button class="add-todo" type="submit">+</button>
        </form>

        <ul class="todo-wrap">
          <li v-for="todo in todos" :key="todo.id" class="todo-item">
            <div class="item-todo">
              <input type="checkbox" />
              <button @click="removeBtn(todo)" class="remove-btn">
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  viewBox="0 0 24 24"
                  fill="red"
                  width="24px"
                  height="24px"
                >
                  <path
                    fill-rule="evenodd"
                    d="M16.5 4.478v.227a48.816 48.816 0 013.878.512.75.75 0 11-.256 1.478l-.209-.035-1.005 13.07a3 3 0 01-2.991 2.77H8.084a3 3 0 01-2.991-2.77L4.087 6.66l-.209.035a.75.75 0 01-.256-1.478A48.567 48.567 0 017.5 4.705v-.227c0-1.564 1.213-2.9 2.816-2.951a52.662 52.662 0 013.369 0c1.603.051 2.815 1.387 2.815 2.951zm-6.136-1.452a51.196 51.196 0 013.273 0C14.39 3.05 15 3.684 15 4.478v.113a49.488 49.488 0 00-6 0v-.113c0-.794.609-1.428 1.364-1.452zm-.355 5.945a.75.75 0 10-1.5.058l.347 9a.75.75 0 101.499-.058l-.346-9zm5.48.058a.75.75 0 10-1.498-.058l-.347 9a.75.75 0 001.5.058l.345-9z"
                    clip-rule="evenodd"
                  />
                </svg>
              </button>
            </div>
            <span class="todo-text">{{ todo.inputField }}</span>
          </li>
        </ul>
        <div class="center">
          <div class="todo-count">
            You have
            <span>{{ todos.length ? todosLength : "no" }}</span> pending tasks
          </div>
          <button
            class="add-todo"
            @click="clearTodos"
            :class="{ 'd-none': show }"
          >
            Clear all
          </button>
        </div>
      </div>
    </div>
  </main>
</template>
<style>
.d-none {
  transition: all 700ms;
  opacity: 0;
  visibility: hidden;
}
.flex {
  display: flex;
  margin-bottom: 20px;
}
.center {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.center .add-todo {
  font-size: 18px;
  padding: 6px 12px;
}
.input-field {
  max-width: 400px;
  width: 100%;
  padding: 10px 15px;
  margin-right: 8px;
  border-radius: 5px;
  border: 1px solid lightgray;
  font-size: 20px;
}
.input-field:focus {
  outline-color: dodgerblue;
}
.add-todo {
  font-size: 2rem;
  padding: 0 12px;
  background-color: blueviolet;
  color: white;
  border: none;
  border-radius: 5px;
}
.addTodo:focus {
  border: none;
}
.todo-count {
  font-weight: 700;
}
.todo-item {
  max-width: 400px;
  padding: 10px 15px;
  border-radius: 5px;
  background-color: #f6f5f5;
  color: #000;
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  font-size: 20px;
}
.remove-btn {
  border: none;
  background-color: transparent;
  margin-left: 8px;
}
.item-todo {
  display: flex;
  align-items: center;
  order: 2;
}
.item-todo:has(input:checked) ~ .todo-text {
  text-decoration: line-through;
}
</style>
