<script lang="ts" setup>
import { computed, ref } from "vue";
import type { PropType } from "vue";
import { get } from "lodash";
import vector from "../assets/Vector.svg";

const props = defineProps({
  header: { type: Array as PropType<Record<string, string>[]>, required: true },
  data: { type: Array, required: true },
  page: {
    type: Number,
    default: () => 1,
  },
  perPage: {
    type: Number,
    default: () => 10,
  },
  sort: {
    type: String,
    default: () => "",
  },
  lastPage: {
    type: Number,
    default: () => 1.
  },
  search: {
    type: String,
    default: () => "",
  }
});

const emit = defineEmits(["pageChange", "sortChange", "perPageChange", "onSearch", "toggleState"]);

const getValue = (
  option: Record<string, string>,
  key: string,
): Record<string, string> | string => {
  return get(option, key);
};

const hasData = computed(() => !!props.data.length);

function onChange({target}: Event) {
  const { value, name } = target as HTMLInputElement;
  const events: Record<string, "pageChange" | "sortChange" | "perPageChange"> = {
    page: "pageChange",
    sort: "sortChange",
    per_page: "perPageChange",
  }
  emit(events[name], value);
}

const productName = ref(props.search);

const searchProduct = computed({
  get: () => {
    return props.search;
  },
  set: (val: string) => {
    productName.value = val;
  }
});

function onSearch() {
  emit("onSearch", productName.value);
}

function toggleState(index: number) {
  emit("toggleState", index);
}
</script>
<template>
<div class="searchable-table">
  <div class="table-container">
    <div class="search-field">
      <div class="form-field">
        <input type="text" placeholder="Find Product" v-model="searchProduct" >
        <button class="btn search-btn" @click="onSearch">Search</button>
      </div>
    </div>
    <form >
      <div class="form-field">
        <input type="text" placeholder="Product Name">
        <button class="btn add-product-btn" disabled>Add Product</button>
      </div>
    </form>
    <div class="list-container">
      <div v-for="(row, index) in data" :key="`row-${index}`" v-if="hasData" class="list-row">
        <div v-for="(col, i) in header" :key="`head-${i}`" :class="col.class">
          <slot :name="`${col.key}`" :row="row" :col="getValue(row, col.key)" :index="index" v-if="$slots[col.key]"></slot>
          <div v-else> {{ getValue(row, col.key) }} </div>
        </div>
        <img :src="vector" class="indicator" @click="toggleState(index)"/>
      </div>
    </div>
  </div>
  <div class="meta">
    <div class="pagination">
      <span>Page</span>
      <select name="page" :value="props.page" @change="onChange">
        <option v-for="i in lastPage" :value="i" :key="i">{{ i }}</option>
      </select>
      <span>of<strong>{{ lastPage }}</strong></span>
    </div>
    <div class="sorting">
      <span>Sort by</span>
      <select name="sort" :value="props.sort" @change="onChange">
        <option value="">Sort Order</option>
        <option value="name">A-Z</option>
        <option value="date">Date</option>
      </select>
    </div>
    <div class="per-page">
      <select name="per_page" :value="props.perPage" @change="onChange">
        <option value="10">10</option>
        <option value="20">20</option>
        <option value="50">50</option>
      </select>
    </div>
  </div>
</div>
</template>
<style>
.searchable-table {
  width: 100%;
  height: 95%;
}

.table-container {
  border-radius: 8px;
  padding: 16px;
  margin-top: 16px;
  height: 84%;
  width: 100%;
  background-color: #FFFFFF;
  overflow: auto;
}
.table-container input {
  height: 38px;
  border: 1px solid #E0E2E4;
  border-radius: 4px;
  padding: 12px 8px;
}

.form-field {
  display: flex;
  flex-direction: row;
  align-items: center;
  width: 100%;
  margin-top: 8px;
}

form input {
  width: 200px;
}

.btn {
  height: 38px;
  padding: 11px 16px;
  font-family: 'Inter';
  font-style: normal;
  font-weight: 400;
  font-size: 12px;
  line-height: 16px;
  border-radius: 4px;
  color: #FFFFFF;
  flex-grow: 2;
}

.search-btn {
  background: #375673;
  margin-left: 8px;
}

.add-product-btn {
  background-color: #377364;
  margin-left: 8px;
  padding: 11px 4px;
}

.list-container {
  margin-top: 8px;
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-around;
}

.list-row {
  height: 48px;
  min-width: 311px;
  display: flex;
  flex-direction: row;
  align-items: center;
  border-bottom: 1px solid #F0F2F4;
  gap: 6px;
}
.indicator {
  color: #252526;
  margin: 0 0 0 6px;
  justify-self: end;
}

input::-webkit-input-placeholder {padding: 12px 8px;}
input:-moz-placeholder {padding: 12px 8px;}
input::-moz-placeholder {padding: 12px 8px;}
input:-ms-input-placeholder {padding: 12px 8px;}

.meta {
  display: flex;
  flex-direction: row;
  align-items: center;
  justify-content: space-between;
  margin: 8px 16px;
}

.meta > .pagination {
  display: flex;
  align-items: center;
  flex-direction: row;
}

.meta > .pagination > select {
  appearance: none;
  padding: 8px;
}
.meta > .pagination  strong{
  padding-left: 1px;
}

.meta > .sorting > select {
  appearance: none;
  padding: 8px;
}

/*tablet*/

@media screen and (max-width: 1180px) and (min-width: 400px) {
.table-container input {
  flex-grow: 3;
}
form input {
  width: unset;
}

.btn {
  flex-grow: 1;
}
}
</style>