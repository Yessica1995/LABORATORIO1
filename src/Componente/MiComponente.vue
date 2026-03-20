<script setup>
import { ref, computed } from 'vue';

const cola = ref([]); 
const miTicket = ref(null); 
const contadorId = ref(101); 
const form = ref({
  nombre: '',
  cuenta: ''
});

const registrarTurno = () => {
  const nuevoTurno = {
    id: contadorId.value++,
    nombre: form.value.nombre,
    cuenta: form.value.cuenta
  };
  cola.value.push(nuevoTurno);
  miTicket.value = nuevoTurno;
  
  
  form.value.nombre = '';
  form.value.cuenta = '';
};


const atenderSiguiente = () => {
  if (cola.value.length > 0) {
    const atendido = cola.value.shift();
    alert(`Llamando al turno #${atendido.id}: ${atendido.nombre}`);
  }
};

const eliminarTurno = (index) => {
  cola.value.splice(index, 1);
};

const formValido = computed(() => {
  return form.value.nombre.trim().length > 3 && form.value.cuenta.trim().length > 4;
});

const posicionEnCola = computed(() => {
  if (!miTicket.value) return 0;
  const index = cola.value.findIndex(t => t.id === miTicket.value.id);
  return index === -1 ? 0 : index;
});
</script>

<template>
  <div class="banco-kay-app">
    <header class="header">
      <h1>Banco KAY 🏦</h1>
      <span class="tag">Proyecto Sostenible: Cero Papel</span>
    </header>

    <main class="grid-container">
      <section class="card">
        <h2>Terminal de Turnos</h2>
        
        <div v-if="!miTicket" class="form-group">
          <p>Ingrese sus datos para obtener un turno digital:</p>
          <input v-model="form.nombre" type="text" placeholder="Nombre completo" />
          <input v-model="form.cuenta" type="text" placeholder="Número de cuenta" />
          <button @click="registrarTurno" :disabled="!formValido" class="btn-primary">
            Generar Ticket
          </button>
        </div>

        <div v-else class="ticket-display">
          <div class="ticket-card">
            <p>Hola, <strong>{{ miTicket.nombre }}</strong></p>
            <small>TU TURNO ES EL:</small>
            <div class="number">#{{ miTicket.id }}</div>
            
            <div class="status">
              <p v-if="posicionEnCola > 0">
                Personas delante de ti: <strong>{{ posicionEnCola }}</strong>
              </p>
              <p v-else class="call-action">
                ¡ES TU TURNO! Pasa a ventanilla.
              </p>
            </div>
            <button @click="miTicket = null" class="btn-link">Nuevo trámite</button>
          </div>
        </div>
      </section>

      <section class="card">
        <h2>Panel de Gestión</h2>
        <button 
          @click="atenderSiguiente" 
          :disabled="cola.length === 0" 
          class="btn-success"
        >
          Llamar Siguiente
        </button>

        <div class="queue-monitor">
          <h3>Cola de espera: {{ cola.length }}</h3>
          <table v-if="cola.length > 0">
            <thead>
              <tr>
                <th>ID</th>
                <th>Cliente</th>
                <th></th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(c, index) in cola" :key="c.id">
                <td>#{{ c.id }}</td>
                <td>{{ c.nombre }}</td>
                <td>
                  <button @click="eliminarTurno(index)" class="btn-del">❌</button>
                </td>
              </tr>
            </tbody>
          </table>
          <p v-else class="empty-msg">No hay clientes esperando.</p>
        </div>
      </section>
    </main>
  </div>
</template>

<style scoped>
.banco-kay-app {
  max-width: 1100px;
  margin: 0 auto;
  padding: 20px;
  font-family: Arial, sans-serif;
}

.header {
  text-align: center;
  margin-bottom: 30px;
  border-bottom: 2px solid #004a99;
  padding-bottom: 10px;
}

.tag {
  color: #27ae60;
  font-weight: bold;
  text-transform: uppercase;
  font-size: 0.8rem;
}

.grid-container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(320px, 1fr));
  gap: 25px;
}

.card {
  background: #ffffff;
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 4px 10px rgba(0,0,0,0.1);
}

h2 { color: #004a99; margin-top: 0; }

input {
  width: 100%;
  padding: 12px;
  margin-bottom: 10px;
  border: 1px solid #ddd;
  border-radius: 6px;
  box-sizing: border-box;
}

button {
  width: 100%;
  padding: 12px;
  border-radius: 6px;
  cursor: pointer;
  font-weight: bold;
  border: none;
}

.btn-primary { background: #004a99; color: white; }
.btn-success { background: #27ae60; color: white; margin-bottom: 20px; }
.btn-primary:disabled { background: #ccc; }

.ticket-card {
  border: 3px dashed #004a99;
  padding: 20px;
  text-align: center;
  background: #f0f7ff;
}

.number {
  font-size: 3.5rem;
  font-weight: bold;
  color: #004a99;
}

.call-action {
  color: #27ae60;
  font-weight: bold;
  animation: pulse 1.5s infinite;
}

.queue-monitor table {
  width: 100%;
  border-collapse: collapse;
}

th, td {
  padding: 10px;
  text-align: left;
  border-bottom: 1px solid #eee;
}

.btn-del {
  background: none;
  width: auto;
  padding: 5px;
}

@keyframes pulse {
  0% { transform: scale(1); }
  50% { transform: scale(1.05); }
  100% { transform: scale(1); }
}
</style>