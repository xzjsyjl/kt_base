<template>
  <button
      :class="buttonClasses"
      @click="$emit('click', $event)"
  >
    <slot></slot>
  </button>
</template>

<script setup>
import { computed } from 'vue';
const props = defineProps({
  variant: {
    type: String,
    default: 'default',
  },
});
const buttonClasses = computed(() => {
  let baseStyles = 'inline-flex items-center justify-center rounded-md font-medium text-sm transition-colors h-10 px-4 py-2';
  const variantStyles = {
    primary: 'bg-indigo-600 text-white hover:bg-indigo-700',
    default: 'border border-gray-300 bg-white text-gray-700 hover:bg-gray-50',
  };
  return `${baseStyles} ${variantStyles[props.variant] || variantStyles.default}`;
});
defineEmits(['click']);
</script>