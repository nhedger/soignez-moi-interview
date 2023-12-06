<script setup lang="ts">
import { ref } from 'vue';
import { v4 as uuidv4 } from 'uuid';
import { useStorage } from '@vueuse/core';
import TodoItem from './TodoItem.vue';
import type { TodoItem as TodoItemType } from '../types';

/**
 * List of todo items
 * 
 * This list is initialized with some default items. We're using local storage
 * to persist the list of items between page reloads. Once the list has been
 * modified, the default items won't be used anymore.
 */
const items = useStorage<TodoItemType[]>('todo-items', [
    { id: uuidv4(), title: 'Create basic todo app', completed: true },
    { id: uuidv4(), title: 'Setup GitHub repository', completed: false },
    { id: uuidv4(), title: 'Deploy demo application on Vercel', completed: false },
    { id: uuidv4(), title: 'Share repository and demo URL', completed: false },
]);

/** Title of the todo item being created */
const title = ref('');

/**
 * Adds a new todo item to the list
 */
const addTodoItem = () => {

    // Don't add empty items
    if (title.value.trim() === '') {
        return;
    }

    // Push the todo item to the list
    items.value.push({
        id: uuidv4(),
        title: title.value,
        completed: false,
    });

    // Reset the title
    title.value = '';
}

/**
 * Removes a todo item from the list
 * 
 * @param id Unique identifier of the todo item
 */
const removeTodoItem = (id: string) => {
    // Find the index of the item
    const index = items.value.findIndex(item => item.id === id);

    // Remove the item from the list
    items.value.splice(index, 1);
}

/**
 * Toggles the completion status of a todo item
 * 
 * @param id Unique identifier of the todo item
 */
const toggleCompletion = (id: string) => {
    // Find the index of the item
    const index = items.value.findIndex(item => item.id === id);

    // Update the item
    items.value[index].completed = !items.value[index].completed;
}
</script>

<template>
    <div class="flex flex-col gap-4 w-96">
        <form @submit.prevent="addTodoItem">
            <input v-model="title" type="text" placeholder="What needs to be done?"
                class="block w-full rounded-md border-0 py-1.5 pl-3 pr-3 text-gray-900 ring-1 ring-inset ring-gray-300 placeholder:text-gray-400 focus:ring-2 focus:ring-inset focus:ring-zinc-600 sm:text-sm sm:leading-6">
        </form>
        <template v-if="items.length > 0">
            <TodoItem v-for="item in items" :key="item.id" :item="item" @removed="removeTodoItem"
                @completionToggled="toggleCompletion" />
                <div class="text-xs text-center text-zinc-400">Items are persisted in your browser's local storage</div>
        </template>
        <div v-else class="px-8 py-12 text-zinc-500 border-2 border-dashed border-zinc-200 rounded-md text-center text-sm">
            Feeling a bit empty? Add a todo item!
        </div>
    </div>
</template>