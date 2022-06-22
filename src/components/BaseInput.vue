<template>
  <div class="input-wrap">
    <label class="mb-1 block" v-if="label">{{ label }}</label>
    <input
      type="text"
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
      :placeholder="placeholder"
      class="py-2 px-1"
      v-bind="$attrs"
    />
  </div>
</template>

<script>
export default {
  inheritAttrs: false,
};
</script>

<script setup>
defineProps({
  placeholder: {
    type: [String, Boolean],
    default: false,
  },
  label: {
    type: [String, Boolean],
    default: false,
  },
  modelValue: {
    type: String,
    default: "",
  },
});
</script>
