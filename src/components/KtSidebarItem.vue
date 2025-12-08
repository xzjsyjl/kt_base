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
      <svg v-if="!isCollapsed" :class="['w-4 h-4 transition-transform duration-200', { 'rotate-90': isSubMenuOpen }]" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7M5 5l7 7-7 7"></path></svg>
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

<script setup lang="ts">
import { ref, computed } from 'vue';
import { useRoute } from 'vue-router';
// 1. 导入类型定义
import type { MenuItem } from '@/types/menu'; // 确保路径正确

// 2. 修正：使用 defineProps 结合 TypeScript 类型字面量
// 显式声明组件名称，这是解决递归组件引用问题的最可靠方法之一
defineOptions({
  name: 'KtSidebarItem'
});

// 3. 使用 defineProps 声明 props 类型和结构
const props = defineProps<{
  item: MenuItem; // 使用导入的 MenuItem 接口，明确数据结构
  isCollapsed: boolean;
}>();


const route = useRoute();
const isSubMenuOpen = ref(false);

const isActive = computed(() => {
  // TypeScript 环境下，item 已经是 MenuItem 类型，可以安全访问 children
  if (props.item.children) {
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


export interface MenuItem {
  /** 菜单项的路由路径 */
  path: string;

  /** 菜单项显示的标题 */
  title: string;

  /** 菜单项的图标 */
  icon?: string;

  /** 子菜单项数组（递归结构） */
  children?: MenuItem[];

  /** 权限标识符（可选） */
  permission?: string;
}
</script>