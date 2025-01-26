<script setup lang="ts">
defineOptions({
  name: 'DomainTableHeader'
});

interface Props {
  itemAlign?: NaiveUI.Align;
  loading?: boolean;
}

defineProps<Props>();

interface Emits {
  (e: 'add'): void;
  (e: 'refresh'): void;
}

const emit = defineEmits<Emits>();

function add() {
  emit('add');
}
function refresh() {
  emit('refresh');
}
</script>

<template>
  <NSpace :align="itemAlign" wrap justify="end" class="lt-sm:w-200px">
    <slot name="prefix"></slot>
    <NButton size="small" ghost type="primary" @click="add">
      <template #icon>
        <icon-ic-round-plus class="text-icon" />
      </template>
      {{ $t('common.add') }}
    </NButton>
    <NButton size="small" @click="refresh">
      <template #icon>
        <icon-mdi-refresh class="text-icon" :class="{ 'animate-spin': loading }" />
      </template>
      {{ $t('common.refresh') }}
    </NButton>
    <slot name="suffix"></slot>
  </NSpace>
</template>

<style scoped></style>
