# Refactorizar Vista `Index.vue`

```vue
<script lang="ts">
import { defineComponent } from 'vue'
import * as Services from '../services/'
import type { Task } from '@/types'

export default defineComponent({
  data() {
    return {
      tasks: [] as Task[],
      pending: false
    }
  },
  mounted() {
    this.getTasks();
  },
  methods: {
    getTasks() {
      this.pending = true
      Services.getTasks()
        .then(response => this.tasks = response.data )
        .catch(
          error => console.log({
            errorCode: error.code, errorMessage: error.message
          })
        )
        .finally(() => this.pending = false)
    },
    removeTask(id: string) {
      this.pending = true
      Services.removeTask(id)
        .then(response => {
          console.log({ statusCode: response.status })
          if (response.status===204)
            this.getTasks();
          })
        .catch(
          error => console.log({
            errorCode: error.code, errorMessage: error.message
          })
        )
        .finally(() => this.pending = false)
    }
  }
})
</script>

<template>
  <div class="container mx-auto">
    <h1 v-if="pending" class="text-2xl" align="center">Loading...</h1>
    <h1 v-else class="text-2xl" align="center">ToDo List</h1>
      
    <router-link
      :to="{name: 'create'}"
      class="btn btn-primary"
      >Create
    </router-link>    
    <table class="min-w-full text-left text-sm font-light">
      <thead class="border-b font-medium dark:border-neutral-500">
        <tr>
          <th class="p-2">ID</th>
          <th class="p-2">Title</th>
          <th class="p-2">Description</th>
          <th class="p-2">Done</th>
          <th class="p-2">Created At</th>
          <th class="p-2">Updated At</th>
          <th class="p-2">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="task in tasks"
          :key="task.id"
          class="border-b dark:border-neutral-500"
        >
          <td class="p-2">{{ task.id }}</td>
          <td class="p-2">{{ task.title }}</td>
          <td class="p-2">{{ task.description }}</td>
          <td class="p-2">{{ task.done }}</td>
          <td class="p-2">{{ task.created_at }}</td>
          <td class="p-2">{{ task.updated_at }}</td>          
          <td>
            <div class="btn-group" role="group">
              <router-link
                :to="{name: 'edit', params: { id: task.id }}"
                class="btn btn-success m-1"
              >Edit
              </router-link>
              <button
                class="btn btn-danger m-1 text-sm"
                @click="removeTask(task.id as string)"
              >
                Delete
              </button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>
    <h4 v-if="tasks.length === 0">Empty list.</h4>
  </div>
</template>
```