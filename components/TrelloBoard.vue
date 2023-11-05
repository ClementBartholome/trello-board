<template>
  <div class="flex items-start overflow-x-auto gap-4">
    <draggable
      v-model="columns"
      group="columns"
      :animation="150"
      handle=".drag-handle"
      item-key="id"
      class="flex gap-4 items-start p-2 flex-wrap"
    >
      <template #item="{ element: column }: { element: Column }">
        <div class="column bg-gray-200 p-5 rounded min-w-[250px]">
          <header class="font-bold mb-4">
            <DragHandle />
            <input
              type="text"
              class="title-input bg-transparent focus:bg-white rounded px-1 w-4/5"
              @keyup.enter="($event.target as HTMLInputElement).blur()"
              @keydown.backspace="
                column.title === ''
                  ? (columns = columns.filter((c) => c.id !== column.id))
                  : null
              "
              v-model="column.title"
            />
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
                    column.tasks = column.tasks.filter((t) => t.id !== task.id)
                  "
                /></div></template
          ></draggable>

          <footer>
            <NewTask @add="column.tasks.push($event)" />
          </footer></div
      ></template>
    </draggable>
    <button
      @click="createColumn"
      class="bg-gray-200 whitespace-nowrap p-2 rounded opacity-50"
    >
      + Add Another Column
    </button>
  </div>
</template>

<script setup lang="ts">
import { ref } from "vue";
import draggable from "vuedraggable";
import { nanoid } from "nanoid";
import type { Column, Task } from "@/types";
const columns = useLocalStorage<Column[]>("trelloBoard", []);

const alt = useKeyModifier("Alt");

watch(
  columns,
  () => {
    // ajax request to save columns to server
  },
  {
    deep: true,
  }
);

function createColumn() {
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
}
</script>
