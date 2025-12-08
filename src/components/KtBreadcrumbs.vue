<template>
  <nav class="flex px-4 py-3 text-gray-700 bg-gray-50 border-y border-gray-200" aria-label="Breadcrumb">
    <ol class="inline-flex items-center space-x-1 md:space-x-3">
      <li class="inline-flex items-center">
        <router-link to="/" class="inline-flex items-center text-sm font-medium hover:text-indigo-600">
          <svg class="w-4 h-4 mr-2" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path d="M10.707 2.293a1 1 0 00-1.414 0l-7 7a1 1 0 001.414 1.414L4 10.414V17a1 1 0 001 1h2a1 1 0 001-1v-2a1 1 0 011-1h2a1 1 0 011 1v2a1 1 0 001 1h2a1 1 0 001-1v-6.586l.293.293a1 1 0 001.414-1.414l-7-7z"></path></svg>
          首页
        </router-link>
      </li>

      <li v-for="(crumb, index) in crumbs" :key="index" class="flex items-center">
        <svg class="w-4 h-4 text-gray-400 mx-1" fill="currentColor" viewBox="0 0 20 20" xmlns="http://www.w3.org/2000/svg"><path fill-rule="evenodd" d="M7.293 14.707a1 1 0 010-1.414L10.586 10 7.293 6.707a1 1 0 011.414-1.414l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414 0z" clip-rule="evenodd"></path></svg>

        <router-link
            v-if="crumb.to"
            :to="crumb.to"
            class="ml-1 text-sm font-medium hover:text-indigo-600 md:ml-2"
        >
          {{ crumb.label }}
        </router-link>
        <span v-else class="ml-1 text-sm font-medium text-gray-500 md:ml-2">
          {{ crumb.label }}
        </span>
      </li>
    </ol>
  </nav>
</template>

<script setup>
import { computed } from 'vue';
import { useRoute } from 'vue-router';

const route = useRoute();

// 核心逻辑：从当前路由及其父路由的 `meta.breadcrumb` 中提取信息
const crumbs = computed(() => {
  let breadcrumbs = [];
  let matched = route.matched; // 获取当前路由的所有匹配记录（包括父级）

  // 从第一个匹配项开始遍历
  matched.forEach((match) => {
    // 确保有 meta 信息且 meta 中有 breadcrumb 字段（例如 { label: '用户设置', to: '/admin/userSet' }）
    if (match.meta && match.meta.breadcrumb) {
      breadcrumbs.push({
        label: match.meta.breadcrumb.label || match.name,
        // 如果是最后一个路径，通常不应该有 to 链接
        to: match.path === route.path ? null : match.meta.breadcrumb.to || match.path,
      });
    }
  });

  return breadcrumbs;
});

// 注意：要让这个组件工作，您需要在路由配置 (index.ts) 的 meta 字段中添加 `breadcrumb` 属性。
// 示例: { path: 'userSet', name: 'userSet', meta: { requiresAuth: true, breadcrumb: { label: '个人设置' } } }
</script>