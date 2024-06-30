<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");
const input_content = ref("");
const input_category = ref("business"); // Initialize with a default value

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return a.createdAt - b.createdAt;
  })
);

watch(name, (newVal) => {
  localStorage.setItem("name", newVal);
});

watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === "") {
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime(),
  });
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  todos.value = JSON.parse(localStorage.getItem("todos")) || [];
});
</script>

<template>
  <main
    class="flex flex-col space-4-6 justify-center items-center bg-slate-300 min-h-screen"
  >
    <section class="mt-2">
      <h2 class="text-2xl">
        What's up,
        <input
          type="text"
          id="name"
          placeholder="Name here"
          v-model="name"
          class="rounded-md bg-slate-300"
        />
      </h2>
    </section>

    <section class="">
      <h3>CREATE A TODO</h3>

      <form class="space-y-6" @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        <input
          type="text"
          name="content"
          id="content"
          placeholder="e.g. make a video"
          v-model="input_content"
          class="rounded-lg h-8"
        />

        <h4 class=" ">Pick a category</h4>
        <div class="flex space-x-20">
          <label>
            <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>

        <input
          class="cursor-pointer text-pink-600"
          type="submit"
          value="Add todo"
        />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <div
          v-for="todo in todos_asc"
          :key="todo.createdAt"
          :class="['todo-item', { done: todo.done }]"
        >
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span
              :class="`bubble ${
                todo.category == 'business' ? 'business' : 'personal'
              }`"
            ></span>
          </label>
          <div class="flex space-x-4">
            <div class="todo-content">
              <input
                type="text"
                v-model="todo.content"
                class="rounded-md h-8"
              />
            </div>

            <div class="actions">
              <button
                class="delete text-red-600 text-2xl"
                @click="removeTodo(todo)"
              >
                Delete
              </button>
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
