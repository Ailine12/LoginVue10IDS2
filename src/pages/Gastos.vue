<template>
  <v-container>
    <h2 class="mb-4">Gestión de Gastos</h2>

    <v-card class="pa-4 mb-4">
      <v-form @submit.prevent="agregarGasto">
        <v-text-field v-model="nuevo.fecha" label="Fecha" type="date" required></v-text-field>
        <v-text-field v-model="nuevo.monto" label="Monto" type="number" required></v-text-field>
        <v-text-field v-model="nuevo.descripcion" label="Descripción" required></v-text-field>
        <v-btn color="primary" type="submit">Agregar</v-btn>
      </v-form>
    </v-card>

    <v-card>
      <v-table>
        <thead>
          <tr>
            <th>Fecha</th>
            <th>Monto</th>
            <th>Descripción</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="gasto in gastos" :key="gasto.id">
            <td>{{ gasto.fecha }}</td>
            <td>${{ gasto.monto }}</td>
            <td>{{ gasto.descripcion }}</td>
            <td>
              <v-btn size="small" color="warning" @click="editarGasto(gasto)">Editar</v-btn>
              <v-btn size="small" color="error" @click="eliminarGasto(gasto.id)">Eliminar</v-btn>
            </td>
          </tr>
        </tbody>
      </v-table>
    </v-card>
  </v-container>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const gastos = ref([])
const nuevo = ref({ fecha: '', monto: '', descripcion: '' })
const apiBase = 'http://localhost:8000/api' // 👈 Ajusta si tu backend usa otro puerto

async function cargarGastos() {
  const { data } = await axios.post(`${apiBase}/gastos`)
  gastos.value = data
}

async function agregarGasto() {
  await axios.post(`${apiBase}/gasto/nuevo`, nuevo.value)
  nuevo.value = { fecha: '', monto: '', descripcion: '' }
  await cargarGastos()
}

async function eliminarGasto(id) {
  await axios.post(`${apiBase}/gasto/eliminar`, { id })
  await cargarGastos()
}

function editarGasto(gasto) {
  nuevo.value = { ...gasto }
}

onMounted(() => {
  cargarGastos()
})
</script>
