<template>
  <li
    v-auto-animate
    v-for="todo in filteredTodos"
    :key="todo.id"
    class="relative rounded-md p-1 flex items-center justify-center"
  >
    <div class="w-full flex items-center justify-between">
      <div class="flex items-center gap-2">
        <span class="flex items-center gap-2">
          <CheckIcon v-if="!todo.completed" @click="toggleTodo(todo.id)" class="h-10 w-10 text-green-500 cursor-pointer duration-200 rounded-full p-2 hover:bg-gray-500/30" />
          <XMarkIcon v-else @click="toggleTodo(todo.id)" class="h-10 w-10 text-red-500 cursor-pointer duration-200 rounded-full p-2 hover:bg-gray-500/30" />
        </span>
        <h3 class="font-medium" :class="[todo.completed ? 'line-through' : 'block']">
          {{ todo.title }}
        </h3>
      </div>
      <div>
        <TrashIcon @click="deleteTodo(todo.id)" class="h-10 w-10 text-red-500 cursor-pointer duration-200 rounded-full hover:bg-gray-500/30 p-2" />
      </div>
    </div>
  </li>
</template>

<script setup lang="ts">
import TodoType from "@/types/TodoType";
import { TrashIcon, CheckIcon, XMarkIcon } from "@heroicons/vue/24/solid";

defineProps<{ filteredTodos: TodoType[] }>();
const emit = defineEmits<{
  (e: "deleteTodo", id: number): void
  (e: "toggleTodo", id: number): void
}>()

function deleteTodo(id: number) {
  emit("deleteTodo", id);
}

function toggleTodo(id: number) {
  emit("toggleTodo", id)
}
</script>