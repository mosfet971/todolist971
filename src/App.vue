<template>
  <div class="container text-left">
    <div class="row">
      <VueDraggable class="col" v-model="list1" animation="150" group="task" @update="onUpdate" @add="onAdd"
        @remove="remove" target=".el_table">
        <table class="table">
          <thead>
            <tr>
              <th scope="col">id</th>
              <th scope="col">name</th>
            </tr>
          </thead>
          <tbody class="el_table">
            <tr v-for="item in list1" :key="item.id">
              <th scope="row">{{ item.id }}</th>
              <td>{{ item.name }}</td>
            </tr>
          </tbody>
        </table>
      </VueDraggable>
      <VueDraggable class="col" v-model="list2" animation="150" group="task" @update="onUpdate" @add="onAdd"
        @remove="remove" target=".el_table">
        <table class="table">
          <thead>
            <tr>
              <th scope="col">id</th>
              <th scope="col">name</th>
            </tr>
          </thead>
          <tbody class="el_table">
            <tr v-for="item in list2" :key="item.id">
              <th scope="row">{{ item.id }}</th>
              <td>{{ item.name }}</td>
            </tr>
          </tbody>
        </table>
      </VueDraggable>
      <VueDraggable class="col" v-model="list3" animation="150" group="task" @update="onUpdate" @add="onAdd"
        @remove="remove" target=".el_table">
        <table class="table">
          <thead>
            <tr>
              <th scope="col">id</th>
              <th scope="col">name</th>
            </tr>
          </thead>
          <tbody class="el_table">
            <tr v-for="item in list3" :key="item.id">
              <th scope="row">{{ item.id }}</th>
              <td>{{ item.name }}</td>
            </tr>
          </tbody>
        </table>
      </VueDraggable>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { VueDraggable } from 'vue-draggable-plus'
const list1 = ref([
  {
    name: 'Joao',
    id: '1'
  },
  {
    name: 'Jean',
    id: '2'
  },
  {
    name: 'Johanna',
    id: '3'
  },
  {
    name: 'Juan',
    id: '4'
  }
])
const list2 = ref([
  {
    name: 'TEEEST',
    id: '10'
  }
])
const list3 = ref([
  {
    name: 'TASSDadas',
    id: '102'
  }
])

function onUpdate() {
  console.log('update')
}
function onAdd() {
  
}
function remove(index) {
  console.log('remove')
}
</script>