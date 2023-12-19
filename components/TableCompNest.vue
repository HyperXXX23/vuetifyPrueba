<template>
  <v-data-table
    v-model:expanded="expanded"
    :headers="headers"
    :items="itemData"
    item-value="name"
    show-expand
  >
    <template v-slot:headers></template>
    <template v-slot:expanded-row="{ columns, item }">
      <tr v-for="el in item.moreNestedItems" :key="el.name">
        <td class="pl-10" :colspan="columns.length / 2">{{ el.name }}</td>
        <td class="text-right" :colspan="columns.length / 2">
          {{ `${el.value} €` }}
        </td>
      </tr>
    </template>
    <template v-slot:bottom></template>
  </v-data-table>
</template>

<script setup>
import { ref } from "vue";

const expanded = ref([]);
const headers = ref([
  {
    title: "",
    align: "start",
    sortable: false,
    key: "name",
  },
  {
    title: "",
    key: `value`,
    align: "end",
    sortable: false,
  },
  { title: "", key: "data-table-expand" },
]);

const props = defineProps({
  itemData: {
    type: Array, // or the appropriate type for your item data
  },
});
</script>
