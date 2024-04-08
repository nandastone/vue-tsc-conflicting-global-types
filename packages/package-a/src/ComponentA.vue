<script setup lang="ts">
defineProps<{
  propA: string;
}>();
</script>

<template>
  <div>ComponentA - {{ propA }}</div>
</template>

<style scoped></style>
