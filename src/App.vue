<script setup lang="ts">
import { ref, computed, nextTick } from "vue";
import { data } from "./data";
import SortableTable from "./components/SortableTable.vue";

const header: Record<string, string>[] = [
  {
    key: "image",
    class: "image"
  },
  {
    key: "status",
    class:"",
  },
  {
    key: "name",
    class: "list-name",
  },
  {
    key: "date",
    class: "list-date"
  }
];

const currentPage = ref(1);

const perPage = ref(10);

const lastPage = computed(() => {
  return treatedData.value.length > perPage.value ? treatedData.value.length / perPage.value : 1;
})

const sortedBy = ref("");

const search = ref("");

const treatedData = ref([...data]);

const selectedData = ref(treatedData.value.slice((currentPage.value -1) * perPage.value, perPage.value));

function formatDate(date: number) {
  const dat = new Date(date);
  return `${dat.getMonth()}/${dat.getDay()}/${dat.getFullYear().toString()[2]}${dat.getFullYear().toString()[3]}`;
}

function pageChange(page: string) {
  currentPage.value = parseInt(page);
  selectedData.value = treatedData.value.slice((currentPage.value -1) * perPage.value, perPage.value * currentPage.value);
}

function sortChange(by: string) {
  currentPage.value = 1;
  sortedBy.value = by;
  if(by === "name") treatedData.value = data.sort((a, b) => a.name.localeCompare(b.name));
  else treatedData.value = data.sort((a, b) => {
    return (a.date == null ? Number.MAX_VALUE : a.date) - (b.date  == null ? Number.MAX_VALUE : b.date) ;
  });
  // get a subarray with a length that is equal or smaller to prePage
  selectedData.value = treatedData.value.slice((currentPage.value -1) * perPage.value, perPage.value)
}

function perPageChange(per: string) {
  currentPage.value = 1;
  perPage.value = parseInt(per);
  // get a subarray with a length that is equal or smaller to prePage
  selectedData.value = treatedData.value.slice((currentPage.value -1) * perPage.value, perPage.value)
}

function onSearch(srch: string) {
  search.value = srch;
  currentPage.value = 1;
  treatedData.value = data.filter(item => (new RegExp(search.value.toLocaleLowerCase())).test(item.name.toLocaleLowerCase()));
  // get a subarray with a length that is equal or smaller to prePage
  selectedData.value = treatedData.value.length < perPage.value ? [...treatedData.value] : treatedData.value.slice((currentPage.value -1) * perPage.value, perPage.value * currentPage.value)
}

const toggleState = (index: number) => {
  const i = (currentPage.value - 1) * perPage.value + index;
  if(treatedData.value[i].status === "red") treatedData.value[i].status = "yellow";
  else if(treatedData.value[i].status === "yellow") treatedData.value[i].status = "green";
  else treatedData.value[i].status = "red";
}

</script>

<template>
<div class="porduct-page">
  <h1 class="product-title">Products</h1>
  <SortableTable :data="selectedData" :header="header" @pageChange="pageChange" @perPageChange="perPageChange" @sortChange="sortChange" @onSearch="onSearch" @toggleState="toggleState" :page="currentPage" :sort="sortedBy" :perPage="perPage" :lastPage="lastPage" :search="search">
      <template v-slot:image="{ col }">
        <img :src="col">
      </template>
      <template v-slot:status="{ col }">
        <div class="state" :class="col"></div>
      </template>
      <template v-slot:name="{ col }">
        <div> {{ col }} </div>
      </template>
      <template v-slot:date="{ col }">
        <div> {{ formatDate(col) }} </div>
      </template>
    </SortableTable>
</div>
</template>

<style>
.porduct-page {
  width: 100%;
  height: 100vh;
  padding: 16px;
  background-color: #ececec;
}

.product-title {
  font-family: 'Bitter';
  font-style: normal;
  font-weight: 500;
  font-size: 24px;
  line-height: 32px;
}


.list-row .image {
  height: 24px;
  width: 24px;
}
.state {
  border-radius: 9999px;
  height: 10px;
  width: 10px;
}

.green {
  background-color: #518103;
}

.red {
  background-color: #C63434;
}

.yellow {
  background-color: #E4D33A;
}

.list-name {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 400;
  font-size: 14px;
  line-height: 22px;
  word-break:break-all;
  flex-grow: 2;
}
.list-date {
  font-family: 'Inter';
  font-style: normal;
  font-weight: 400;
  font-size: 14px;
  line-height: 22px;
}
</style>
