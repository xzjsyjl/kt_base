<template>
  <header class="h-16 flex items-center justify-between bg-white shadow-md px-6">
    <div class="flex items-center">
      <slot name="left"></slot>
    </div>

    <div class="flex items-center space-x-4">
      <button class="text-gray-500 hover:text-indigo-600 transition-colors">
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9"></path></svg>
      </button>

      <div class="relative">
        <button
            @click="isUserMenuOpen = !isUserMenuOpen"
            class="flex items-center space-x-2 p-1 rounded-full hover:bg-gray-100 transition duration-150"
        >
          <img class="h-8 w-8 rounded-full object-cover" src="https://via.placeholder.com/150" alt="User Avatar">
          <span class="text-sm font-medium text-gray-700">User Name</span>
        </button>

        <div v-if="isUserMenuOpen" class="absolute right-0 mt-2 w-48 bg-white rounded-md shadow-lg py-1 z-50">
          <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">设置</a>
          <a href="#" class="block px-4 py-2 text-sm text-gray-700 hover:bg-gray-100">退出登录</a>
        </div>
      </div>
    </div>
  </header>
</template>

<script setup>
import { ref } from 'vue';

const isUserMenuOpen = ref(false);
</script>