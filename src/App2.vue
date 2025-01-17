<script setup>
import { computed, handleError, ref, watch } from "vue";

defineProps({
  msg: String,
});
// =========================================
const arr = ref([1, 2, 3, 4, 5]);
// =========================================
const counter = computed(() => 1 + arr.value.length);
// =========================================
const object1 = ref([
  {
    name: "Andi",
    age: 20,
  },
  {
    name: "susi",
    age: 29,
  },
  {
    name: "budi",
    age: 18,
  },
]);
// =========================================
const ubahName = () => {
  object1.value[0].name = "kutilang";
};
// =========================================
const x = ref(0);
const y = ref(0);
// =========================================

const handleForm = computed(() => {
  console.log("Nama : ", fullname.value.firstName, fullname.value.lastName);
  nama.value = "";
});
// =========================================
const messege = computed(() => {
  console.log("typing...");
  return fullname.value.firstName.toLowerCase() === "rizki"
    ? "Selamat Datang"
    : "Saya Tidak Kenal";
});
// =========================================
const Active = computed(() => {
  console.log("Active...");
});
// =========================================
const nama = ref("");

// =========================================

const fullname = ref({
  firstName: "Andi",
  lastName: "Kutilang",
});
// =========================================

watch(
  () => object1.value[0].name,
  (newName, oldName) => {
    console.log("Old Name: ", oldName, "New Name: ", newName);
    alert(" Berhasil di ubah dari " + oldName + " Menjadi " + newName);
  }
);
</script>
<template>
  <nav class="mt-10">
    <div class="flex items-center justify-center">
      <form @submit.prevent="handleForm">
        <input
          type="text"
          placeholder="firstName"
          v-model.lazy="fullname.firstName"
          class="peer p-2 m-2 border transition-all duration-300 ease-out border-blue-500 rounded-md peer bg-gray-100 outline-green-600"
        />
        <input
          type="text"
          placeholder="lastName"
          v-model.lazy="fullname.lastName"
          class="peer p-2 m-2 border transition-all duration-300 ease-out border-blue-500 rounded-md peer bg-gray-100 outline-green-600"
        />
        <button
          type="submit"
          class="peer-focus:bg-green-500 p-2 m-2 border transition-all duration-300 ease-out border-blue-500 rounded-md bg-blue-500"
        >
          Submit
        </button>
      </form>
    </div>
  </nav>
  <header class="text-center mt-3 text-2xl">
    <h1>{{ x }}: {{ y }}</h1>
  </header>
  <div
    class="flex flex-col justify-center items-center m-2"
    @mousemove="Coordinate"
  >
    <div class="p-3 bg-gray-200 w-60 rounded-md shadow-lg text-center my-4">
      <table class="m-2 p-4">
        <thead>
          <tr class="">
            <th class="p-2">No</th>
            <th class="p-2">Nama</th>
            <th class="p-2">Umur</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(people, index) in object1">
            <td class="p-2">{{ index + 1 }}</td>
            <td class="p-2">{{ people.name }}</td>
            <td class="p-2">{{ people.age }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div
      class="bg-blue-600 p-2 w-32 h-10 rounded-md transition-colors duration-300 ease-out hover:bg-blue-500 text-center m-2 cursor-pointer"
      v-on:click="ubahName"
    >
      <div v-if="fullname.firstName !== '' || fullname.lastName !== ''">
        {{ fullname.firstName }} {{ fullname.lastName }}
      </div>
      <div v-else>Ubah Nama</div>
    </div>
    <div v-bind:class="{ box: true }">
      <div>{{ messege }}</div>
      <div>{{ Active }}</div>
    </div>
  </div>
</template>

<style>
.box {
  margin-top: 20px;
  width: 150px;
  padding: 10px;
  text-align: center;
  align-items: center;
  background-color: saddlebrown;
  border-radius: 10px;
}
</style>
