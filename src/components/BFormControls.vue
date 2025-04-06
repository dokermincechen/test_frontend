<template>
  <div>
    <label :for="uniqueId" class="form-label">{{ label }}</label>
    <input
      :id="uniqueId"
      :type="type"
      class="form-control"
      :placeholder="placeholder"
      :value="modelValue"
      @input="$emit('update:modelValue', ($event.target as HTMLInputElement)?.value)"
    />
  </div>
</template>

<script lang="ts">
import { defineComponent, computed } from 'vue';

export default defineComponent({
  props: {
    modelValue: [String, Number],
    label: String,
    type: {
      type: String,
      default: 'text',
    },
    id: String,
    placeholder: String,
  },
  emits: ['update:modelValue'],
  setup(props, { emit }) {
    const uniqueId = computed(() => props.id || `input-${Math.random().toString(36).substr(2, 9)}`);

    return {
      uniqueId,
    };
  },
});
</script>
