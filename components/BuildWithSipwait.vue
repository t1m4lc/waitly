<script setup lang="ts">
const props = defineProps<{
  projectId?: string;
}>();

</script>

<template>
  <button as-child class="custom-button">
    <NuxtLink :to="`/?ref=page_${projectId}`">
      Built with 🚢 Shipwait
    </NuxtLink>
  </button>
</template>

<style scoped>
.custom-button {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  border-radius: 0.375rem;
  font-size: 0.875rem;
  font-weight: 500;
  border: 1px solid var(--border-color, #e2e8f0);
  background-color: var(--bg-color, white);
  padding: 0.5rem 1rem;
  color: black;
  box-shadow: 0 1px 3px 0 rgba(0, 0, 0, 0.1);
  transition: all 250ms ease-in-out 50ms;
  z-index: 2147483647;
  /* maximum z-index to avoid hidding */
}

.custom-button:hover {
  transform: translateY(-0.25rem) scale(1.03);
  background-color: var(--hover-bg, #f8f9fa);
}
</style>