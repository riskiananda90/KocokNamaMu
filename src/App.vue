<template>
  <div
    class="min-h-screen flex flex-col items-center justify-center py-10"
    style="background-image: url('/bg.jpg')"
  >
    <div
      class="flex flex-col lg:flex-row w-full max-w-6xl mx-auto px-4 gap-6 lg:gap-10"
    >
      <!-- Input Form -->
      <div
        class="bg-gray-400 flex-1 p-6 lg:p-10 rounded-lg border border-gray-200 shadow-lg"
      >
        <div
          class="bg-white p-6 rounded-md hover:shadow-xl transition-all duration-500 ease-in-out"
        >
          <h1 class="text-3xl lg:text-5xl text-center font-bold">
            KOCOK NAMA KAMU
          </h1>
        </div>

        <div
          class="bg-gray-300 p-4 rounded-md hover:shadow-xl transition-all duration-500 mt-6 border border-gray-200"
        >
          <form
            @submit.prevent="handleSubmit"
            class="flex flex-col sm:flex-row gap-3"
          >
            <div class="flex-1">
              <label for="nameInput" class="sr-only">Masukkan Nama</label>
              <input
                id="nameInput"
                type="text"
                v-model="nameInput"
                placeholder="Masukkan nama..."
                class="w-full p-3 border border-gray-400 text-xl rounded-md focus:outline-none focus:ring-2 focus:ring-green-500 h-14 font-semibold capitalize"
                :aria-invalid="!!errorMessage"
                :aria-describedby="errorMessage ? 'error-message' : undefined"
              />
            </div>
            <button
              type="submit"
              class="bg-green-500 p-3 text-xl border border-gray-400 rounded-md hover:bg-green-400 transition-colors w-full sm:w-28 h-14 text-white font-semibold disabled:opacity-50 disabled:cursor-not-allowed"
              :disabled="isSubmitting"
            >
              {{ isSubmitting ? "Adding..." : "Add" }}
            </button>
          </form>
        </div>

        <!-- Error Message -->
        <div
          v-if="errorMessage"
          role="alert"
          id="error-message"
          class="flex items-center gap-2 bg-red-100 text-red-700 px-4 py-2 mt-3 rounded-lg animate-bounce"
        >
          <svg
            xmlns="http://www.w3.org/2000/svg"
            class="h-5 w-5"
            viewBox="0 0 20 20"
            fill="currentColor"
          >
            <path
              fill-rule="evenodd"
              d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z"
              clip-rule="evenodd"
            />
          </svg>
          {{ errorMessage }}
        </div>
      </div>

      <!-- Names Grid -->
      <div
        class="flex-1 lg:flex-2 bg-slate-200 p-6 lg:p-10 rounded-lg border border-gray-200 shadow-lg"
      >
        <div
          class="grid grid-cols-2 sm:grid-cols-3 lg:grid-cols-4 gap-4 max-h-[60vh]"
        >
          <TransitionGroup name="name-list">
            <button
              v-for="(name, index) in names"
              :key="name"
              @click="deleteName(index)"
              class="bg-gray-400 px-4 py-2 rounded-lg border border-gray-200 shadow-lg font-semibold capitalize hover:bg-gray-500 hover:text-white transition-colors text-center truncate"
              :title="`Delete ${name}`"
            >
              {{ name }}
            </button>
          </TransitionGroup>
        </div>

        <div v-if="names.length === 0" class="text-center text-gray-500 mt-4">
          No names added yet
        </div>
      </div>
    </div>

    <!-- Shuffle Button -->
    <TransitionRoot appear :show="names.length > 1">
      <button
        @click="handleShuffle"
        class="mt-8 bg-green-500 text-white px-8 py-4 rounded-lg shadow-lg font-bold text-xl hover:bg-green-400 transition-all transform hover:scale-105 focus:outline-none focus:ring-2 focus:ring-green-500 focus:ring-offset-2"
      >
        Kocok Nama
      </button>
    </TransitionRoot>

    <!-- Result Modal -->
    <TransitionRoot appear :show="!!shuffleResult">
      <Dialog as="div" @close="closeModal" class="relative z-10">
        <TransitionChild
          enter="ease-out duration-300"
          enter-from="opacity-0"
          enter-to="opacity-100"
          leave="ease-in duration-200"
          leave-from="opacity-100"
          leave-to="opacity-0"
        >
          <div class="fixed inset-0 bg-black bg-opacity-25" />
        </TransitionChild>

        <div class="fixed inset-0">
          <div class="flex min-h-full items-center justify-center p-4">
            <TransitionChild
              enter="ease-out duration-300"
              enter-from="opacity-0 scale-95"
              enter-to="opacity-100 scale-100"
              leave="ease-in duration-200"
              leave-from="opacity-100 scale-100"
              leave-to="opacity-0 scale-95"
            >
              <DialogPanel
                class="w-full max-w-md transform overflow-hidden rounded-2xl bg-white p-6 text-center align-middle shadow-xl transition-all"
              >
                <DialogTitle
                  as="h3"
                  class="text-2xl font-bold leading-6 text-gray-900 mb-4"
                >
                  Hasil Kocok Nama
                </DialogTitle>
                <div class="mt-4">
                  <p class="text-3xl font-bold text-green-600">
                    {{ shuffleResult }}
                  </p>
                </div>
                <div class="mt-6">
                  <button
                    @click="closeModal"
                    class="bg-gray-200 px-4 py-2 rounded-md hover:bg-gray-300 transition-colors"
                  >
                    Tutup
                  </button>
                </div>
              </DialogPanel>
            </TransitionChild>
          </div>
        </div>
      </Dialog>
    </TransitionRoot>
  </div>
</template>

<script setup>
import { ref } from "vue";
import {
  TransitionRoot,
  TransitionChild,
  Dialog,
  DialogPanel,
  DialogTitle,
} from "@headlessui/vue";

const names = ref([]);
const nameInput = ref("");
const errorMessage = ref("");
const isSubmitting = ref(false);
const shuffleResult = ref("");

const validateName = (name) => {
  if (!name.trim()) {
    return "Nama tidak boleh kosong";
  }
  if (name.trim().length < 3) {
    return "Nama minimal 3 karakter";
  }
  if (names.value.includes(name.trim())) {
    return "Nama sudah ada";
  }
  return "";
};

const handleSubmit = async () => {
  isSubmitting.value = true;
  errorMessage.value = validateName(nameInput.value);

  if (!errorMessage.value) {
    names.value.push(nameInput.value.trim());
    nameInput.value = "";
  }

  isSubmitting.value = false;
};

const deleteName = (index) => {
  if (confirm("Yakin ingin menghapus nama ini?")) {
    names.value.splice(index, 1);
  }
};

const handleShuffle = () => {
  const randomIndex = Math.floor(Math.random() * names.value.length);
  shuffleResult.value = names.value[randomIndex];
};

const closeModal = () => {
  shuffleResult.value = "";
};
</script>

<style scoped>
.name-list-enter-active,
.name-list-leave-active {
  transition: all 0.5s ease;
}

.name-list-enter-active,
.name-list-leave-to {
  opacity: 0;
  transform: translateY(30px);
}

.name-list-move {
  transition: transform 0.5s ease;
}
</style>
