<template>
  <div class="container">
    <h1>Calculadora API</h1>

    <div class="campo">
      <label>Operador 1</label>
      <input v-model="operador1" type="text" placeholder="Ej: 10" />
    </div>

    <div class="campo">
      <label>Operación</label>
      <select v-model="operacion">
        <option value="">Seleccionar operación</option>
        <option value="suma">Suma</option>
        <option value="resta">Resta</option>
        <option value="multiplicacion">Multiplicación</option>
        <option value="division">División</option>
      </select>
    </div>

    <div class="campo">
      <label>Operador 2</label>
      <input v-model="operador2" type="text" placeholder="Ej: 5" />
    </div>

    <button @click="calcular" :disabled="cargando">
      {{ cargando ? 'Calculando...' : 'Calcular' }}
    </button>

    <div v-if="respuesta !== null" :class="['resultado', respuesta.ok ? 'ok' : 'error']">
      <div class="label">{{ respuesta.ok ? 'Resultado' : 'Error' }}</div>
      <div v-if="respuesta.ok" class="valor">{{ respuesta.resultado }}</div>
      <div v-else class="msg-error">{{ respuesta.error }}</div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const operador1 = ref('')
const operador2 = ref('')
const operacion = ref('')
const respuesta = ref(null)
const cargando = ref(false)

const API_URL = 'http://localhost/recuperacion/api/calcular.php'

async function calcular() {
  respuesta.value = null

  if (!operador1.value.trim() || !operador2.value.trim() || !operacion.value) {
    respuesta.value = { ok: false, error: 'Datos incompletos. Completa todos los campos.' }
    return
  }

  cargando.value = true
  try {
    const body = new URLSearchParams({
      operador1: operador1.value.trim(),
      operador2: operador2.value.trim(),
      operacion: operacion.value
    })

    const res = await fetch(API_URL, {
      method: 'POST',
      headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
      body: body.toString()
    })

    respuesta.value = await res.json()
  } catch (e) {
    respuesta.value = { ok: false, error: 'No se pudo conectar con la API.' }
  } finally {
    cargando.value = false
  }
}
</script>

<style scoped>
* { box-sizing: border-box; margin: 0; padding: 0; }

body {
  background: #000;
}

.container {
  max-width: 400px;
  margin: 40px auto;
  padding: 0 16px;
  font-family: Arial, sans-serif;
  color: #fff;
}

h1 {
  font-size: 1.2rem;
  margin-bottom: 24px;
}

.campo {
  margin-bottom: 14px;
}

label {
  display: block;
  margin-bottom: 4px;
  color: #ccc;
  font-size: 0.85rem;
}

input, select {
  width: 100%;
  padding: 8px 10px;
  background: #111;
  border: 1px solid #444;
  color: #fff;
  font-size: 0.95rem;
}

button {
  width: 100%;
  padding: 10px;
  background: #fff;
  color: #000;
  border: none;
  font-size: 0.95rem;
  cursor: pointer;
  margin-top: 4px;
}

button:hover { background: #ddd; }
button:disabled { opacity: 0.5; cursor: default; }

.resultado {
  margin-top: 20px;
  padding: 14px;
  border: 1px solid #444;
}

.resultado.ok { border-color: #4a4; }
.resultado.error { border-color: #a44; }

.label {
  font-size: 0.75rem;
  color: #aaa;
  margin-bottom: 6px;
}

.valor { font-size: 1.3rem; color: #fff; }
.msg-error { color: #e08080; }
</style>
