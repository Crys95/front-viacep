<template>
  <div class="bg-white dark:bg-gray-800 shadow-lg rounded-lg p-6 space-y-6 transition">
    <h2 class="text-2xl font-bold text-blue-700 dark:text-blue-300 text-center">
      Consultar Endereço por CEP
    </h2>

    <div>
      <label for="cep" class="block mb-2 text-sm font-medium text-gray-700 dark:text-gray-300">
        CEP
      </label>
      <input
        v-model="cep"
        @input="mascararCep"
        @keyup.enter="buscarCep"
        type="text"
        maxlength="9"
        id="cep"
        placeholder="Digite o CEP (ex: 06412-030)"
        class="w-full p-3 border rounded focus:outline-none focus:ring-2 focus:ring-blue-500 dark:bg-gray-700 dark:border-gray-600 dark:text-white"
      />
      <button
        @click="buscarCep"
        class="mt-3 w-full bg-blue-600 text-white py-2 rounded hover:bg-blue-700 transition"
      >
        Buscar
      </button>
    </div>

    <div v-if="endereco" class="space-y-2 border-t pt-4 border-gray-200 dark:border-gray-700">
      <div class="text-gray-700 dark:text-gray-300">
        <span class="font-semibold text-blue-700 dark:text-blue-300">CEP:</span>
        {{ endereco.cep }}
      </div>
      <div class="text-gray-700 dark:text-gray-300">
        <span class="font-semibold text-blue-700 dark:text-blue-300">Logradouro:</span>
        {{ endereco.logradouro }}
      </div>
      <div class="text-gray-700 dark:text-gray-300">
        <span class="font-semibold text-blue-700 dark:text-blue-300">Bairro:</span>
        {{ endereco.bairro }}
      </div>
      <div class="text-gray-700 dark:text-gray-300">
        <span class="font-semibold text-blue-700 dark:text-blue-300">Localidade:</span>
        {{ endereco.localidade }}
      </div>
      <div class="text-gray-700 dark:text-gray-300">
        <span class="font-semibold text-blue-700 dark:text-blue-300">Estado:</span>
        {{ endereco.estado }}
      </div>
      <div class="text-gray-700 dark:text-gray-300">
        <span class="font-semibold text-blue-700 dark:text-blue-300">Região:</span>
        {{ endereco.regiao }}
      </div>
    </div>

    <div v-if="erro" class="text-red-600 dark:text-red-400 text-center">
      {{ erro }}
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'

const API = import.meta.env.VITE_API_BASE_URL

const cep = ref('')
const endereco = ref(null)
const erro = ref('')

function mascararCep() {
  cep.value = cep.value
    .replace(/\D/g, '')
    .replace(/^(\d{5})(\d)/, '$1-$2')
    .slice(0, 9)
}

async function buscarCep() {
  erro.value = ''
  endereco.value = null

  const cepLimpo = cep.value.replace(/\D/g, '')
  if (cepLimpo.length !== 8) {
    erro.value = 'CEP inválido. Digite 8 números.'
    return
  }

  try {
    const response = await axios.get(`${API}/api/cep/via-cep?cep=${cepLimpo}`)
    endereco.value = response.data
  } catch (err) {
    erro.value = 'CEP não encontrado ou erro na consulta.'
  }
}
</script>
