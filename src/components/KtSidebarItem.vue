<template>
  <router-link
      v-if="!item.children"
      :to="item.path"
      :class="['flex items-center rounded-md p-3 text-sm font-medium transition duration-150',
             isActive ? 'bg-indigo-600 text-white' : 'text-gray-300 hover:bg-gray-700']"
  >
    <i :class="['w-5 h-5 mr-3', isCollapsed ? 'mx-auto' : '']">{{ item.icon }}</i>
    <span v-if="!isCollapsed">{{ item.title }}</span>
  </router-link>

  <div v-else class="text-gray-300">
    <div
        @click="toggleSubMenu"
        :class="['flex items-center justify-between rounded-md p-3 text-sm font-medium cursor-pointer transition duration-150 hover:bg-gray-700',
               isActive ? 'bg-gray-700' : '']"
    >
      <div class="flex items-center">
        <i :class="['w-5 h-5 mr-3', isCollapsed ? 'mx-auto' : '']">{{ item.icon }}</i>
        <span v-if="!isCollapsed">{{ item.title }}</span>
      </div>
      <svg v-if="!isCollapsed" :class="['w-4 h-4 transition-transform duration-200', { 'rotate-90': isSubMenuOpen }]" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"></path></svg>
    </div>

    <div v-if="!isCollapsed" :class="['ml-4 overflow-hidden transition-all duration-300', isSubMenuOpen ? 'max-h-96' : 'max-h-0']">
      <KtSidebarItem
          v-for="child in item.children"
          :key="child.path"
          :item="child"
          :is-collapsed="isCollapsed"
          class="pl-4"
      />
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import { useRoute } from 'vue-router';

const props = defineProps({
  item: {
    type: Object,
    required: true,
  },
  isCollapsed: {
    type: Boolean,
    default: false,
  },
});

const route = useRoute();
const isSubMenuOpen = ref(false);

const isActive = computed(() => {
  if (props.item.children) {
    // 检查子路由中是否有当前激活的路由
    return props.item.children.some(child => route.path.startsWith(child.path));
  }
  return route.path.startsWith(props.item.path);
});

if (isActive.value && props.item.children) {
  isSubMenuOpen.value = true;
}

const toggleSubMenu = () => {
  if (!props.isCollapsed) {
    isSubMenuOpen.value = !isSubMenuOpen.value;
  }
};
</script>