<script setup lang="ts">
import { NButton, NDrawer, NDrawerContent, NSpace } from 'naive-ui';
import NginxConfigEditor from './nginx-config-editor.vue';
defineOptions({
  name: 'DomainConfigDrawer'
});
const visible = defineModel<boolean>('visible', {
  default: false
});
const title = defineModel<string>('title', {
  default: ''
});
const nginxConfig = defineModel<string>('nginx-config', {
  default: 'No config read'
});
const newDomain = defineModel<boolean>('new-domain', {
  default: false
});
function closeDrawer() {
  visible.value = false;
}
function clickSubmit() {
  console.log('click submit');
}
function clickTest() {
  console.log('click test');
}
function clickReload() {
  console.log('click reload');
}
</script>

<template>
  <NDrawer v-model:show="visible" display-directive="show" :width="660">
    <NDrawerContent :title="title" :native-scrollbar="false" closable>
      <NForm>
        <NFormItem v-if="newDomain" label="域名" show-require-mark="true">
          <NInput placeholder="请输入域名" />
        </NFormItem>
        <NFormItem label="代理配置">
          <NginxConfigEditor v-model:config="nginxConfig" v-model:domain_name="title" />
        </NFormItem>
      </NForm>
      <NSpace>
        <NButton type="primary" @click="clickSubmit">提交</NButton>
        <NButton type="error" @click="closeDrawer">取消</NButton>
        <NButton type="warning" @click="clickTest">测试</NButton>
        <NButton type="success" @click="clickReload">加载</NButton>
      </NSpace>
    </NDrawerContent>
  </NDrawer>
</template>

<style lang="scss" scoped></style>
