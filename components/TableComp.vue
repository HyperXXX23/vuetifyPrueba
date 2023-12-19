<template>
  <v-data-table
    v-model:expanded="expanded"
    :headers="headers"
    :items="items"
    item-value="id"
    show-expand
  >
    <template v-slot:top>
      <v-toolbar flat>
        <v-toolbar-title>Categoria 1 Nombre</v-toolbar-title>
        <v-toolbar-title class="text-right mr-14">
          {{ `${totalSum} €` }}
        </v-toolbar-title>
      </v-toolbar>
    </template>
    <template v-slot:expanded-row="{ columns, item }">
      <tr v-for="el in item.nestedItems" :key="el.id">
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
    title: "Nombre",
    align: "start",
    sortable: false,
    key: "nameCat",
  },
  {
    title: "Gastos",
    key: `money`,
    align: "end",
    sortable: false,
  },
  { title: "", key: "data-table-expand" },
]);

const sumaMoney = (items) => {
  return items.reduce((total, item) => {
    // Verificar si 'item.money' está definido y no es null
    if (item && item.money) {
      // Extraer el valor numérico de money
      const moneyValue = parseFloat(item.money.replace("€", "").trim());

      // Verificar si es un número válido antes de sumarlo
      if (!isNaN(moneyValue)) {
        return total + moneyValue;
      }
    }

    return total;
  }, 0);
};

const items = ref([
  // ... Tus datos aquí ...
  {
    id: "001",
    nameCat: "Cat1.1",
    nestedItems: [
      {
        name: "Concepto 1",
        value: 534,
      },
      {
        name: "Concepto 2",
        value: 200,
      },
    ],
  },
  {
    id: "002",
    nameCat: "Cat1.2",
    nestedItems: [
      {
        name: "Concepto 3",
        value: 431,
      },
      {
        name: "Concepto 4",
        value: 225,
      },
    ],
  },
  {
    id: "003",
    nameCat: "Cat1.3",
    nestedItems: [
      {
        name: "Concepto 5",
        value: 632,
      },
      {
        name: "Concepto 6",
        value: 153,
      },
    ],
  },
]);

watch(
  () => items.value,
  () => {
    totalSum.value = sumaMoney(items.value);
  }
);
//inicializo a 0 el valor de la suma total
const totalSum = ref(0);

onMounted(() => {
  // Calcular y asignar la propiedad 'money' para cada elemento en 'items'
  items.value = items.value.map((item) => {
    const money = item.nestedItems.reduce(
      (sum, nestedItem) => sum + nestedItem.value,
      0
    );
    return { ...item, money: `${money} €` };
  });
  //const totalSum = sumaMoney(items.value);
});

//uso un watcher para asegurarme que cuando el valor de la propiedad money se actualize me calcule el total
</script>

<style>
.prueba {
  color: brown;
  font-size: 20px;
  border: 2px solid red;
}
</style>
