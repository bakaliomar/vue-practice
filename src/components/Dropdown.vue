<template>
  <div>
    <select name="states" id="states">
      <option v-for="state in data"  :value="state" :key="state">
        <div :class="state"></div>
      </option>
    </select>
  </div>

</template>
<script lang="ts" setup>
import { ref } from 'vue';
import type { PropType } from 'vue';

const props = defineProps({
  data: {
    type: Array as PropType<string[]>,
    required: true,
  },
  modelValue: {
    type: String,
    default: () => "",
  }
});

const emit = defineEmits(["update:modalValue"]);
</script>
<style>
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
</style>