<script setup lang="ts">
import { ref, watch, reactive } from 'vue';

// =========================================================
// 导出类型定义
// =========================================================

/** 控件类型 */
export type FieldType = 'text' | 'number' | 'textarea' | 'select' | 'date';

/** 单个表单字段的配置 */
export type FormField = {
  prop: string; // 字段在 formData 中的路径，支持 'a.b.c'
  label: string;
  type: FieldType;
  // 其他原生 HTML 属性
  attrs?: Record<string, any> & {
    options?: Array<{ value: string | number, label: string }>; // 仅用于 select
  };
};

/** 单条校验规则 */
export type ValidationRule = {
  required?: boolean;
  message: string;
  pattern?: RegExp;
  validator?: (value: any) => boolean | Promise<boolean>;
};

/** 完整的校验规则对象 */
export type FormRules = Record<string, ValidationRule[]>;

/** 组件暴露给父组件的方法类型 */
export type DynFormExpose = { // 重命名为 DynFormExpose
  validate: () => Promise<Record<string, any> | null>;
  resetFields: () => void;
};

// =========================================================
// 组件核心逻辑
// =========================================================

const props = defineProps<{
  fields: FormField[];
  modelValue: Record<string, any>;
  rules: FormRules;
}>();

const emit = defineEmits<{
  (e: 'update:modelValue', value: Record<string, any>): void;
}>();

const formRef = ref<HTMLFormElement>();
const formData = reactive<Record<string, any>>({ ...props.modelValue });
const initialData = { ...props.modelValue };
const errors = reactive<Record<string, string>>({});

// --- 工具函数：处理嵌套属性 ---

function getPropValue(obj: Record<string, any>, prop: string): any {
  return prop.split('.').reduce((o, k) => (o ? o[k] : undefined), obj);
}

function setPropValue(obj: Record<string, any>, prop: string, value: any): void {
  const keys = prop.split('.');
  let o: Record<string, any> = obj;
  for (let i = 0; i < keys.length - 1; i++) {
    if (!o[keys[i]]) o[keys[i]] = {};
    o = o[keys[i]];
  }
  o[keys[keys.length - 1]] = value;
}

// --- 数据同步 ---

watch(() => props.modelValue, val => Object.assign(formData, val), { deep: true });
watch(formData, val => emit('update:modelValue', val), { deep: true, immediate: true });


// --- 校验逻辑 ---

async function validateField(prop: string): Promise<boolean> {
  const rules = props.rules[prop];
  const value = getPropValue(formData, prop);

  if (!rules) {
    errors[prop] = '';
    return true;
  }

  for (const rule of rules) {
    if (rule.required && (value === undefined || value === null || value === '')) {
      errors[prop] = rule.message;
      return false;
    }

    if (rule.pattern && !rule.pattern.test(String(value))) {
      errors[prop] = rule.message;
      return false;
    }

    if (rule.validator) {
      const isValid = await rule.validator(value);
      if (!isValid) {
        errors[prop] = rule.message;
        return false;
      }
    }
  }

  errors[prop] = '';
  return true;
}

async function validate(): Promise<Record<string, any> | null> {
  let allValid = true;
  for (const field of props.fields) {
    const fieldValid = await validateField(field.prop);
    if (!fieldValid) {
      allValid = false;
    }
  }

  if (allValid) {
    return formData;
  } else {
    return null;
  }
}

function resetFields() {
  Object.keys(errors).forEach(key => errors[key] = '');
  Object.assign(formData, initialData);
}

// --- 暴露方法 ---

defineExpose<DynFormExpose>({ // 使用重命名的 DynFormExpose
  validate,
  resetFields,
});

// 通用输入框的 Tailwind Class
const baseInputClasses = [
  'w-full', 'px-3', 'py-2', 'border', 'border-gray-300', 'rounded-md',
  'text-sm', 'transition-colors', 'duration-200', 'focus:outline-none',
  'focus:ring-2', 'focus:ring-blue-500', 'focus:border-blue-500'
];

</script>

<template>
  <form
      ref="formRef"
      @submit.prevent
      class="p-5 border border-gray-300 rounded-lg shadow-sm bg-white"
  >
    <div
        v-for="item in fields"
        :key="item.prop"
        class="flex flex-col mb-5"
    >
      <label
          :for="item.prop"
          class="font-semibold mb-1 text-gray-700"
      >
        {{ item.label }}:
      </label>

      <div class="relative">

        <template v-if="['text', 'number', 'date'].includes(item.type)">
          <input
              :id="item.prop"
              :type="item.type"
              :value="getPropValue(formData, item.prop)"
              @input="val => { setPropValue(formData, item.prop, val.target.value); validateField(item.prop); }"
              v-bind="item.attrs"
              :class="[...baseInputClasses, { '!border-red-500': errors[item.prop] }]"
          />
        </template>

        <template v-else-if="item.type === 'textarea'">
          <textarea
              :id="item.prop"
              :value="getPropValue(formData, item.prop)"
              @input="val => { setPropValue(formData, item.prop, val.target.value); validateField(item.prop); }"
              v-bind="item.attrs"
              :class="[...baseInputClasses, 'resize-y', { '!border-red-500': errors[item.prop] }]"
          ></textarea>
        </template>

        <template v-else-if="item.type === 'select'">
          <select
              :id="item.prop"
              :value="getPropValue(formData, item.prop)"
              @change="val => { setPropValue(formData, item.prop, val.target.value); validateField(item.prop); }"
              v-bind="item.attrs"
              :class="[...baseInputClasses, { '!border-red-500': errors[item.prop] }]"
          >
            <option value="" disabled selected>{{ item.attrs?.placeholder || `请选择 ${item.label}` }}</option>
            <option
                v-for="opt in item.attrs?.options || []"
                :key="opt.value"
                :value="opt.value"
            >
              {{ opt.label }}
            </option>
          </select>
        </template>

        <div v-if="errors[item.prop]" class="text-red-500 text-xs mt-1">
          {{ errors[item.prop] }}
        </div>
      </div>
    </div>
  </form>
</template>