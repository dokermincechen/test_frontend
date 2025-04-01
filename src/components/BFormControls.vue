<template>
  <div>
    <label :for="uniqueId" class="form-label">{{ label }}</label>
    <input
      :id="uniqueId"
      :type="type"
      class="form-control"
      :placeholder="placeholder"
      :value="modelValue"
      @input="$emit('update:modelValue', $event.target.value)"
    />
  </div>
</template>

<script setup lang="ts">
import { computed, defineProps, defineEmits } from 'vue';

const props = defineProps({
  modelValue: [String, Number],
  label: String,
  type: {
    type: String,
    default: 'text',
  },
  id: String,
  placeholder: String,
});

const emit = defineEmits(['update:modelValue']);

const uniqueId = computed(() => props.id || `input-${Math.random().toString(36).substr(2, 9)}`);
</script>

<style lang="scss" scoped>
</style>
