<script setup lang="ts">
import { ref } from 'vue';
import Codemirror from 'codemirror-editor-vue3';
// @types/codemirror
import type { Editor, EditorConfiguration } from 'codemirror';
// language
import 'codemirror/mode/nginx/nginx.js';
import 'codemirror/theme/material.css';

defineOptions({
  name: 'NginxConfigEditor'
});

const cminstance = ref<Editor>();

const cmOptions: EditorConfiguration = {
  mode: 'nginx',
  lineWrapping: true,
  theme: 'material'
};

const config = defineModel<string>('config', {
  default: 'No config read'
});

async function onReady(cm: Editor) {
  cminstance.value = cm;
}

function onFocus(cm: Editor, event: FocusEvent) {
  console.log('onFocus', cm, event);
  cm.getDoc().on('beforeChange', (instance: Doc, obj: EditorChange) => {
    console.log('beforeChange', instance, obj);
  });
}
</script>

<template>
  <Codemirror v-model:value="config" :options="cmOptions" border @ready="onReady" @focus="onFocus"></Codemirror>
</template>

<style lang="scss" scoped></style>
