<script setup lang="ts">
import { onMounted, computed, ref, watch } from "vue";
import TodoType from "@/types/TodoType";
import TodoItem from "@/components/TodoItem.vue";
import { TabGroup, TabList, Tab } from "@headlessui/vue";
const newTodoTerm = ref<string>("");

const inputError = ref<boolean>(false);
const todos = ref<TodoType[]>([]);
const nextId = ref<number>(1);
const filter = ref<"all" | "active" | "completed">("all");

function addTodo(): void {
  if (
    newTodoTerm.value &&
    !todos.value.map((todo) => todo.title).includes(newTodoTerm.value)
  ) {
    todos.value.push({
      id: nextId.value,
      title: newTodoTerm.value,
      completed: false,
    });
    nextId.value++;
    newTodoTerm.value = "";
    localStorage.setItem("todos", JSON.stringify(todos.value));
  } else {
    inputError.value = true;
    setTimeout(() => (inputError.value = false), 1500);
    return;
  }
  return;
}

function deleteTodo(id: number): void {
  todos.value = todos.value.filter((todo) => todo.id !== id);
}

function toggleTodo(id: number) {
  todos.value = todos.value.map((todo) => {
    if (todo.id === id) {
      todo.completed = !todo.completed;
    }
    return todo;
  });
}

const filteredTodos = computed((): TodoType[] => {
  return filter.value === "active"
    ? todos.value.filter((todo) => !todo.completed)
    : filter.value === "completed"
    ? todos.value.filter((todo) => todo.completed)
    : todos.value;
});

function changeTabs(index: number) {
  index === 1
    ? (filter.value = "active")
    : index === 2
    ? (filter.value = "completed")
    : (filter.value = "all");
}

function clearCompleted(): void {
  todos.value = todos.value.filter((todo) => !todo.completed);
}

onMounted((): void => {
  const savedTodos = localStorage.getItem("todos");
  if (savedTodos) {
    todos.value = JSON.parse(savedTodos);
    nextId.value = todos.value.length
      ? Math.max(...todos.value.map((t) => t.id)) + 1
      : 1;
  }
});

watch(todos, (newTodos): void => {
  localStorage.setItem("todos", JSON.stringify(newTodos));
});
</script>

<template>
  <div v-auto-animate class="container max-w-3xl w-full py-4">
    <form
      @submit.prevent="addTodo"
      class="flex items-center gap-4 sticky top-0 z-50 bg-"
    >
      <input
        type="text"
        class="outline-none w-full h-full border duration-200 p-3 rounded-md shadow-sm"
        :class="[
          inputError
            ? 'animate-shake border-red-500'
            : 'border-transparent focus:border-b-black/30',
        ]"
        v-model.trim="newTodoTerm"
        placeholder="Create a new todo..."
      />
      <button
        type="submit"
        class="rounded-md py-2.5 px-5 bg-blue-500 text-white hover:bg-blue-600 duration-200 shadow-sm"
      >
        Add
      </button>
    </form>
    <ul
      role="list"
      v-auto-animate
      v-if="filteredTodos.length !== 0"
      class="shadow-sm rounded-xl bg-white p-2 ring-white/60 ring-offset-2 ring-offset-blue-400 focus:outline-none focus:ring-2 mt-4 divide-y space-y-1"
    >
      <TodoItem
        :filteredTodos="filteredTodos"
        @deleteTodo="deleteTodo"
        @toggleTodo="toggleTodo"
      />
    </ul>
    <div
      v-else
      class="my-4 font-medium text-lg p-3 rounded-md bg-white w-auto mx-auto"
    >
      <h2 class="text-center">No items found</h2>
    </div>
    <div
      class="w-full max-w-3xl px-2 py-4 sm:px-0 mx-auto flex items-center md:flex-row justify-between flex-col gap-3"
      v-if="todos.length"
    >
      <div class="md:contents flex items-center justify-between w-full">
        <h3 class="whitespace-nowrap p-2">{{ todos.length }} items left</h3>
        <button
          @click="clearCompleted"
          class="whitespace-nowrap md:order-3 order-none px-2 py-2.5 rounded-lg duration-200 hover:bg-blue-900/10 font-medium"
        >
          Clear completed
        </button>
      </div>
      <TabGroup @change="changeTabs">
        <TabList
          v-model="filter"
          class="flex space-x-1 rounded-xl bg-blue-900/30 p-1 shadow-sm w-full"
        >
          <Tab as="template" v-slot="{ selected }">
            <button
              value="all"
              :class="[
                'w-full rounded-lg py-2.5 text-sm font-medium leading-5 duration-100',
                'ring-white/60 ring-offset-2 ring-offset-blue-400 focus:outline-none focus:ring-2',
                selected
                  ? 'bg-white text-blue-700 shadow'
                  : 'text-blue-100 hover:bg-white/[0.12] hover:text-white',
              ]"
            >
              All
            </button>
          </Tab>
          <Tab as="template" v-slot="{ selected }">
            <button
              value="active"
              :class="[
                'w-full rounded-lg py-2.5 text-sm font-medium leading-5 duration-100',
                'ring-white/60 ring-offset-2 ring-offset-blue-400 focus:outline-none focus:ring-2',
                selected
                  ? 'bg-white text-blue-700 shadow'
                  : 'text-blue-100 hover:bg-white/[0.12] hover:text-white',
              ]"
            >
              Active
            </button>
          </Tab>
          <Tab as="template" v-slot="{ selected }">
            <button
              value="completed"
              :class="[
                'w-full rounded-lg py-2.5 text-sm font-medium leading-5 duration-100',
                'ring-white/60 ring-offset-2 ring-offset-blue-400 focus:outline-none focus:ring-2',
                selected
                  ? 'bg-white text-blue-700 shadow'
                  : 'text-blue-100 hover:bg-white/[0.12] hover:text-white',
              ]"
            >
              Completed
            </button>
          </Tab>
        </TabList>
      </TabGroup>
    </div>
  </div>
</template>
