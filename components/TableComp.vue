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
        <v-toolbar-title>Categoria 1</v-toolbar-title>
        <v-toolbar-title class="text-right mr-14">
          {{ `${totalSum} €` }}
        </v-toolbar-title>
      </v-toolbar>
    </template>
    <template v-slot:expanded-row="{ columns, item }">
      <tr>
        <td :colspan="columns.length">
          <TableCompNest :itemData="item.nestedItems" />
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
    value: 0,
    nestedItems: [
      {
        name: "Concepto 1.1",
        value: 0,
        moreNestedItems: [
          {
            name: "Subconcepto 1.1.1",
            value: 450,
          },
          {
            name: "Subconcepto 1.1.2",
            value: 239,
          },
        ],
      },
      {
        name: "Concepto 1.2",
        value: 0,
        moreNestedItems: [
          {
            name: "Subconcepto 1.2.1",
            value: 450,
          },
          {
            name: "Subconcepto 1.2.2",
            value: 239,
          },
        ],
      },
    ],
  },
  {
    id: "002",
    nameCat: "Cat1.2",
    value: 0,
    nestedItems: [
      {
        name: "Concepto 2",
        value: 0,
        moreNestedItems: [
          {
            name: "Subconcepto 2.1.1",
            value: 352,
          },
          {
            name: "Subconcepto 2.1.2",
            value: 467,
          },
        ],
      },
    ],
  },
  {
    id: "003",
    nameCat: "Cat1.3",
    value: 0,
    nestedItems: [
      {
        name: "Concepto 3.1",
        value: 0,
        moreNestedItems: [
          {
            name: "Subconcepto 3.1.1",
            value: 250,
          },
          {
            name: "Subconcepto 3.1.2",
            value: 284,
          },
        ],
      },
      {
        name: "Concepto 3.2",
        value: 0,
        moreNestedItems: [
          {
            name: "Subconcepto 3.2.1",
            value: 250,
          },
          {
            name: "Subconcepto 3.2.2",
            value: 284,
          },
          {
            name: "Subconcepto 3.3.3",
            value: 684,
          },
        ],
      },
    ],
  },
]);

//Actualiza el value de nestedItem con la suma de los valores de la parde baja del arbol
const updateNestedItems = (items) => {
  items.forEach((item) => {
    item.nestedItems.forEach((nestedItem) => {
      nestedItem.value = nestedItem.moreNestedItems.reduce(
        (sum, moreNestedItem) => sum + moreNestedItem.value,
        0
      );
    });
  });
};

//Actualiza el value de cada item con la suma de todos los values de nestedItem
const updateValueItems = (items) => {
  items.forEach((item) => {
    item.value = item.nestedItems.reduce(
      (sum, nestedItem) => sum + nestedItem.value,
      0
    );
  });
};
//uso un watcher para asegurarme que cuando el valor de la propiedad money se actualize me calcule el total
watch(
  () => items.value,
  () => {
    totalSum.value = sumaMoney(items.value);
  }
);
//inicializo a 0 el valor de la suma total
const totalSum = ref(0);

onMounted(() => {
  // Calcular y asignar la propiedad 'money' para cada elemento en 'items'\
  updateNestedItems(items.value);
  updateValueItems(items.value);

  if (Array.isArray(items.value)) {
    items.value = items.value.map((item) => {
      const money = item.nestedItems.reduce(
        (sum, nestedItem) => sum + nestedItem.value,
        0
      );
      return { ...item, money: `${money} €` };
    });
  }
});
</script>
