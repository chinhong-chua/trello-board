<script setup lang="ts">
import type { Column } from "~/types";
import { nanoid } from "nanoid";
const columns = ref<Column[]>([
  {
    id: nanoid(),
    title: "Backlog",
    tasks: [
      {
        id: nanoid(),
        title: "Create marketing landing page",
        createdAt: new Date(),
      },
      {
        id: nanoid(),
        title: "Develop new feature",
        createdAt: new Date(),
      },
      {
        id: nanoid(),
        title: "Fix a bug on page",
        createdAt: new Date(),
      },
    ],
  },
  {
    id: nanoid(),
    title: "Selected for Dev",
    tasks: [],
  },
  {
    id: nanoid(),
    title: "In Progress",
    tasks: [],
  },
  {
    id: nanoid(),
    title: "QA",
    tasks: [],
  },
  {
    id: nanoid(),
    title: "Completed",
    tasks: [],
  },
]);
</script>

<template>
  <div class="flex gap-4 overflow-x-auto items-start">
    <div v-for="column in columns" :key="column.id" class="column bg-gray-200 p-5 rounded min-w-[250px]">
      <header>
        {{ column.title }}
      </header>
      <p v-for="task in column.tasks" :key="task.id">
        {{ task.title }}
      </p>
    </div>
  </div>
</template>
