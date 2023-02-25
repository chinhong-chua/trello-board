<script setup lang="ts">
import type { Column, ID, Task } from "~/types";
import { nanoid } from "nanoid";
import draggable from "vuedraggable";
// const columns = ref<Column[]>([
//   {
//     id: nanoid(),
//     title: "Backlog",
//     tasks: [
//       {
//         id: nanoid(),
//         title: "Create marketing landing page",
//         createdAt: new Date(),
//       },
//       {
//         id: nanoid(),
//         title: "Develop new feature",
//         createdAt: new Date(),
//       },
//       {
//         id: nanoid(),
//         title: "Fix a bug on page",
//         createdAt: new Date(),
//       },
//     ],
//   },
//   {
//     id: nanoid(),
//     title: "Selected for Dev",
//     tasks: [],
//   },
//   {
//     id: nanoid(),
//     title: "In Progress",
//     tasks: [],
//   },
//   {
//     id: nanoid(),
//     title: "QA",
//     tasks: [],
//   },
//   {
//     id: nanoid(),
//     title: "Completed",
//     tasks: [],
//   },
// ]);

const defaultColumns = [
  {
    title: "Backlog",
    id: nanoid(),
    tasks: [
      {
        title: "Create marketing landing page",
        createdAt: new Date(),
        id: nanoid(),
      },
      {
        title: "Develop cool new feature",
        createdAt: new Date(),
        id: nanoid(),
      },
      { title: "Fix page loading bug", createdAt: new Date(), id: nanoid() },
    ],
  },
  { title: "In Progress", id: nanoid(), tasks: [] },
  { title: "Testing", id: nanoid(), tasks: [] },
  { title: "Completed", id: nanoid(), tasks: [] },
];
const columns = useLocalStorage<Column[]>("trelloBoard", defaultColumns);

// watch(
//   columns,
//   () => {
//     // api request in future
//   },
//   {
//     deep: true,
//   }
// );
// const resetColumns = () => {
//   columns.value = defaultColumns;
// };
// onBeforeUnmount(() => {
//   localStorage.removeItem("trelloBoard");
//   window.addEventListener("unload", resetColumns);
// });



const alt = useKeyModifier("Alt");

const createColumn = () => {
  const column: Column = {
    id: nanoid(),
    title: "",
    tasks: [],
  };
  columns.value.push(column);
  nextTick(() => {
    (
      document.querySelector(
        ".column:last-of-type .title-input"
      ) as HTMLInputElement
    ).focus();
  });
};

const handleClose = (id: ID) => {
  columns.value = columns.value.filter((c) => c.id != id);
};
</script>

<template>
  <div class="flex items-start overflow-x-auto gap-4 pb-3">
    <draggable
      v-model="columns"
      group="columns"
      :animation="150"
      handle=".drag-handle"
      item-key="id"
      class="flex gap-4 items-start"
    >
      <template #item="{ element: column }: { element: Column }">
        <div class="column bg-gray-200 p-5 rounded min-w-[250px]">
          <header class="font-bold mb-4">
            <DragHandle />
            <input
              class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5"
              @keyup.enter="($event.target as HTMLInputElement).blur()"
              @keydown.backspace="
                column.title === ''
                  ? (columns = columns.filter((c) => c.id != column.id))
                  : null
              "
              type="text"
              v-model="column.title"
            />
            <button class="float-right" @click="handleClose(column.id)">
              x
            </button>
          </header>
          <draggable
            v-model="column.tasks"
            :group="{ name: 'tasks', pull: alt ? 'clone' : true }"
            handle=".drag-handle"
            :animation="150"
            item-key="id"
          >
            <template #item="{ element: task }: { element: Task }">
              <div>
                <TrelloBoardTask
                  :task="task"
                  @delete="
                    column.tasks = column.tasks.filter((t) => t.id !== $event)
                  "
                />
              </div>
            </template>
          </draggable>

          <footer>
            <NewTask @add="column.tasks.push($event)" />
          </footer>
        </div>
      </template>
    </draggable>
    <button
      @click="createColumn"
      class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50"
    >
      + Add Another Column
    </button>
  </div>
</template>
