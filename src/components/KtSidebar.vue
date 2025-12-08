<template>
  <aside
      :class="[
      'fixed top-0 left-0 h-full bg-gray-800 text-white transition-all duration-300 z-40',
      isCollapsed ? 'w-16' : 'w-64'
    ]"
  >
    <div class="p-4 flex items-center justify-center h-16 border-b border-gray-700">
      <h1 v-if="!isCollapsed" class="text-xl font-bold">Admin Panel</h1>
      <h1 v-else class="text-xl font-bold">A P</h1>
    </div>

    <nav class="p-2 space-y-1 overflow-y-auto" style="height: calc(100vh - 4rem);">
      <slot :isCollapsed="isCollapsed"></slot>
    </nav>

    <div class="absolute bottom-0 left-0 w-full p-2 border-t border-gray-700">
      <button
          @click="toggleCollapse"
          class="w-full py-2 px-3 rounded-md hover:bg-gray-700 transition duration-150 flex items-center justify-center text-gray-300"
          title="Toggle Menu"
      >
        <svg v-if="isCollapsed" class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 5l7 7-7 7M5 5l7 7-7 7"></path></svg>
        <svg v-else class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 19l-7-7 7-7m8 14l-7-7 7-7"></path></svg>
      </button>
    </div>
  </aside>
</template>

<script setup>
import { ref } from 'vue';

const isCollapsed = ref(false);

const toggleCollapse = () => {
  isCollapsed.value = !isCollapsed.value;
};
</script>