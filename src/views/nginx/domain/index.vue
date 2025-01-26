<script setup lang="tsx">
import { onMounted, ref } from 'vue';
import axios from 'axios';
import DomainConfigDrawer from './modules/domain-config-drawer.vue';
import DomainTableHeader from './modules/domain-table-header.vue';

const data = ref([
  { domain: 'test-domain.tenserpay.xyz', ip: ['10.16.0.109', '10.16.0.110'] },
  { domain: 'test-domain-more-prj.tenserpay.xyz', ip: ['10.16.0.109', '10.16.0.110'] },
  { domain: 'rabbitmq-sit-pay.tenserpay.xyz', ip: ['10.16.0.109', '10.16.0.110'] }
]);
const drawerVisible = ref(false);
const drawerTitle = ref('example.com');
const drawerConfig = ref('No config read');
const drawerNewDomain = ref(false);
const domainFilter = ref('');
const ipFilter = ref('');
// 点击编辑按钮，打开抽屉，获取域名配置
const editDomain = async (name: string) => {
  drawerVisible.value = true;
  drawerTitle.value = name;
  drawerConfig.value = 'No config read';
  drawerNewDomain.value = false;
  try {
    const response = await axios.get(`http://10.16.155.116:11602/domain/${drawerTitle.value}`);
    console.log('response:', response);
    drawerConfig.value = response.data.config;
  } catch (error) {
    console.error('Failed to fetch domain data:', error);
    drawerConfig.value = config.value;
  }
};

const deleteDomain = async (name: string) => {
  try {
    console.log('delete domain:', name);
    // const response = await axios.delete(`http://10.16.155.116:11602/domain/${name}`);
    // console.log('response:', response);
    // 删除成功后刷新数据
    // refreshdata();
  } catch (error) {
    console.error('Failed to delete domain:', error);
  }
};

// 点击header里面的新增按钮
async function handleAdd() {
  drawerVisible.value = true;
  drawerTitle.value = '新建域名';
  drawerConfig.value = 'server {\n    listen 80;\n    server_name example.com;\n}';
  drawerNewDomain.value = true;
}

async function refreshdata() {
  try {
    let url;
    url = 'http://10.16.155.116:11602/domains';
    // 判断是否有搜索条件
    if (domainFilter.value !== '' || ipFilter.value !== '') {
      // 有搜索条件，拼接url + ?
      // 例如：http://10.16.155.116:11602/domains?domain=test-domain.tenserpay.xyz&ip=10.16.0.109
      url += '?';
      if (domainFilter.value !== '') {
        url += `domain=${domainFilter.value}&`;
      }
      if (ipFilter.value !== '') {
        url += `ip=${ipFilter.value}&`;
      }
      // 去掉最后一个&
      url = url.slice(0, -1);
    }

    const response = await axios.get(url);
    console.log('response:', response);
    data.value = [];
    for (const item of response.data) {
      const domain_content = {
        domain: item.domain,
        ip: item.ip
      };
      data.value.push(domain_content);
    }
  } catch (error) {
    console.error('Failed to fetch domain data:', error);
  }
}

async function resetSearchParams() {
  domainFilter.value = '';
  ipFilter.value = '';
  await refreshdata();
}

onMounted(async () => {
  await refreshdata();
});
</script>

<template>
  <div class="min-h-500px flex-col-stretch gap-16px overflow-hidden lt-sm:overflow-auto">
    <NCard :bordered="false" size="small" class="card-wrapper">
      <NCollapse :title="$t('common.search')">
        <NCollapseItem :title="$t('common.search')" name="search">
          <NSpace vertical>
            <NSpace class="container">
              <NGradientText type="info">域名:</NGradientText>
              <NInput v-model:value="domainFilter" placeholder="请输入域名" />
            </NSpace>
            <NSpace class="container">
              <NGradientText type="info">ip:</NGradientText>
              <NInput v-model:value="ipFilter" placeholder="请输入Nginx server ip" />
            </NSpace>
            <NSpace class="search-button">
              <NButton type="primary" @click="refreshdata">搜索</NButton>
              <NButton @click="resetSearchParams">重置</NButton>
            </NSpace>
          </NSpace>
        </NCollapseItem>
      </NCollapse>
    </NCard>

    <NCard title="域名列表" :bordered="false" size="small" class="scrollable-card sm:flex-1-hidden card-wrapper">
      <template #header-extra>
        <DomainTableHeader @add="handleAdd" @refresh="refreshdata" />
      </template>
      <NTable :bordered="false" :single-line="false">
        <thead>
          <tr>
            <th>Name</th>
            <th>Nginx server ip</th>
            <th>操作</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in data" :key="index">
            <td>{{ item.domain }}</td>
            <td>{{ item.ip }}</td>
            <td>
              <NSpace>
                <NButton ghost type="primary" @click="editDomain(item.domain)">编辑</NButton>
                <NButton ghost type="error" @click="deleteDomain(item.domain)">删除</NButton>
              </NSpace>
            </td>
          </tr>
        </tbody>
      </NTable>
      <DomainConfigDrawer
        v-model:visible="drawerVisible"
        v-model:title="drawerTitle"
        v-model:nginx-config="drawerConfig"
        v-model:new-domain="drawerNewDomain"
      />
    </NCard>
  </div>
</template>

<style scoped>
.scrollable-card {
  max-height: 800px; /* 超过此高度会出现滚动条 */
  overflow-y: auto;
}
.search-button {
  margin-left: 20px;
}
.container {
  display: flex;
  align-items: center; /* 垂直居中对齐 */
  gap: 10px; /* 域名和输入框之间的间距 */
}
</style>
