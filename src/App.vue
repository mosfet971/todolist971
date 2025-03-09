<template>
  <button class="btn btn-primary" style="margin: 0.3em; width: 9em !important; height: 2.5em !important; overflow: hidden;" v-on:click="addTask()">Добавить задачу</button>
  <VueDraggable class="btn btn-danger" style="margin: 0.3em; width: 9em !important; height: 2.5em !important; overflow: hidden;" group="task" v-model="trashList">Корзина</VueDraggable>
  <p style="display: inline;">Перетаскивайте задачи между списками</p>
  <div class="container text-left mpzero">
    <div class="row mpzero" style="width: 70em;">
      <VueDraggable class="col mpzero" v-model="lists[0]" animation="150" group="task"
        target=".el_table">
        <table class="table table-light">
          <thead>
            <th>
            <td>Входящие:</td>
            </th>
          </thead>
          <tbody class="el_table">
            <tr v-for="item in lists[0]" :key="item.id">
              <td>{{ item.name }}</td>
            </tr>
          </tbody>
        </table>
      </VueDraggable>
      <VueDraggable class="col mpzero" v-model="lists[1]" animation="150" group="task"
        target=".el_table">
        <table class="table table-light">
          <thead>
            <th>
            <td>Проекты:</td>
            </th>
          </thead>
          <tbody class="el_table">
            <tr v-for="item in lists[1]" :key="item.id">
              <td>{{ item.name }}</td>
            </tr>
          </tbody>
        </table>
      </VueDraggable>
      <VueDraggable class="col mpzero" v-model="lists[2]" animation="150" group="task"
        target=".el_table">
        <table class="table table-light">
          <thead>
            <th>
            <td>Свободные задачи:</td>
            </th>
          </thead>
          <tbody class="el_table">
            <tr v-for="item in lists[2]" :key="item.id">
              <td>{{ item.name }}</td>
            </tr>
          </tbody>
        </table>
      </VueDraggable>
    </div>
    <div class="row mpzero" style="width: 100vw; overflow-x: scroll; display: block; height: 45vh !important; white-space:nowrap; overflow-y: hidden;">
      <VueDraggable v-for="i in daysListIds" :key="i" class="col mpzero dayList" v-model="lists[i]" animation="150" group="task"
        target=".el_table">
        <table class="table table-light">
          <thead>
            <th>
            <td>{{getFormattedDate(listIdToDate[i])}}</td>
            </th>
          </thead>
          <tbody class="el_table">
            <tr v-for="item in lists[i]" :key="item.id">
              <td>{{ item.name }}</td>
            </tr>
          </tbody>
        </table>
      </VueDraggable>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted, watchEffect } from 'vue'
import { VueDraggable } from 'vue-draggable-plus'

const trashList = ref([]);

let daysNumber = 15;
let mainListsNumber = 3;

let initListsValue = [];
let daysListIds = [];
for (let i = 0; i < mainListsNumber+daysNumber; i++) {
  initListsValue.push([]);
  if (i>2) {
    daysListIds.push(i);
  }
}

let listIdToDate = ref([]);

function generateDates() {
  let num = 0;
  for (const i of daysListIds) {
    let idate = new Date();
    idate.setDate(idate.getDate() + num);
    listIdToDate.value[i] = idate;
    num++;
  }
}

generateDates();

const lists = ref(initListsValue);

watchEffect(()=>{
  for (let i = 0; i < lists.value.length; i++) {
    lists.value[i] = lists.value[i].filter((v)=>v.name!=="")
  }
  for (let i = 0; i < lists.value.length; i++) {
    if (lists.value[i].length<30) {
      let need = 30 - lists.value[i].length;
      for (let j = 0; j < need; j++) {
        lists.value[i].push({ id: crypto.randomUUID(), name: "" });
      }
    }
    if (lists.value[i].length>30) {
      let need = lists.value[i].length - 30;
      for (let j = 0; j < need; j++) {
        for (let k = 0; k < lists.value[i].length; k++) {
          if (need > 0) {
            if(lists.value[i][k].name == "") {
              lists.value[i].splice(k, 1);
              need--;
            }
          }
        }
      }
    }
  }
});

watch(() => lists.value, (newValue, oldValue) => {
  localStorage.setItem("lists", JSON.stringify({ val: newValue }));
}, { deep: true });

onMounted(() => {
  if (localStorage.getItem("lists") == null) {
    console.log(localStorage.getItem("lists") == null);
    localStorage.setItem("lists", JSON.stringify({
      val: initListsValue
    }));
    localStorage.setItem("prevAcessDate", Date.now().toString());
  }

  let savedLists = JSON.parse(localStorage.getItem("lists")).val;

  let dateDiff = dateDiffInDays(new Date(parseInt(localStorage.getItem("prevAcessDate"))), new Date());
  for(let i = 0; i < dateDiff; i++) {
    console.log(i);
    for(let j = mainListsNumber; j < mainListsNumber+daysNumber+1; j++) {
      if(j==3) {
        for(const k of savedLists[j]) {
          savedLists[2].push(k);
        }
        continue;
      }
      if (j==(mainListsNumber+daysNumber)) {
        savedLists[j-1] = [];
        continue;
      }

      savedLists[j-1] = savedLists[j];
    }
  }
  generateDates();
  localStorage.setItem("prevAcessDate", Date.now().toString());

  lists.value = savedLists;
});


function remove(index) {
  //console.log(index);
}
function addTask() {
  let name = prompt("Введите текст задачи: ");

  if (!!name && name.length > 0) {
    lists.value[0].unshift({ id: crypto.randomUUID(), name: name });
  }
}

function dateDiffInDays(a, b) {
  const _MS_PER_DAY = 1000 * 60 * 60 * 24;
  const utc1 = Date.UTC(a.getFullYear(), a.getMonth(), a.getDate());
  const utc2 = Date.UTC(b.getFullYear(), b.getMonth(), b.getDate());

  return Math.floor((utc2 - utc1) / _MS_PER_DAY);
}


function getFormattedDate(date) {
  var year = date.getFullYear();

  var month = (1 + date.getMonth()).toString();
  month = month.length > 1 ? month : '0' + month;

  var day = date.getDate().toString();
  day = day.length > 1 ? day : '0' + day;
  
  let days = ['ВС', 'ПН', 'ВТ', 'СР', 'ЧТ', 'ПТ', 'СБ'];

  return day + '.' + month + '.' + year + ", " + days[date.getDay()];
}

</script>
<style scoped>
html,
body {
  padding: 0;
  margin: 0;
}

.col {
  height: 40vh !important;
  overflow-y: scroll;
  text-align: center;
  align-items: center;
  vertical-align: middle;
  margin: 1em !important;
  overflow-x: hidden; 
}

tr {
  font-size: 80%;
  height: 2em !important;
}

td {
  padding: 0;
  padding-top: 0.1em;
  width: 100em;
}

th {
  padding: 0;
}

.mpzero {
  margin: 0;
  padding: 0;
}

.dayList {
  width: 20em;
  display: inline-block;
  overflow-y: hidden;
}
</style>