<script setup lang="ts">
import { ComponentA } from "package-a";

defineProps<{
  propB: string;
}>();
</script>

<template>
  <div>
    <ComponentA propA="From Component B" />
    <div>ComponentB - {{ propB }}</div>
  </div>
</template>

<style scoped></style>
