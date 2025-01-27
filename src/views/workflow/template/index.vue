<script setup lang="ts">
import { useRouterPush } from '@/hooks/common/router';

const { routerPushByKey } = useRouterPush();
</script>

<template>
  <div class="min-h-500px flex-col-stretch gap-16px overflow-hidden lt-sm:overflow-auto">
    <NCard :bordered="false" size="small" class="card-wrapper">
      <!-- routerPushByKey use name in router.ts -->
      <NButton type="primary" size="small" @click="routerPushByKey('workflow_details')">新增</NButton>
    </NCard>
  </div>
</template>

<style scoped></style>
