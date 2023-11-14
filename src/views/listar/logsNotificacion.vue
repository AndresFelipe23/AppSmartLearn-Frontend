<template>
    <div>
      <v-data-table
        :headers="headers"
        :items="logs"
        class="elevation-1"
      >
        <template v-slot:items="props">
          <td>{{ props.item.receptor }}</td>
          <td class="text-xs-right">{{ props.item.fecha }}</td>
          <td class="text-xs-right">{{ props.item.detalles }}</td>
        </template>
      </v-data-table>
    </div>
  </template>
  
  <script>
  import axios from 'axios'
  
  export default {
    data () {
      return {
        logs: [], 
        headers: [
          { text: 'Receptor', value: 'receptor' },
          { text: 'Fecha de envío', value: 'fecha' },
          { text: 'Detalles', value: 'detalles' },
        ],
      }
    },
    methods: {
      cargarLogs() {
        axios
          .get('http://localhost:3001/logs')
          .then((response) => {
            this.logs = response.data;
            console.log('logs', this.logs);
          })
          .catch((error) => {
            console.error('Error al obtener logs', error);
          });
      },
    //   getStatusClass(statusHttp) {
    //     return {
    //         'text-xs-right': true,
    //         'bg-red': statusHttp === '500',
    //         'bg-green': statusHttp === '200',
    //     }
    //     },
    },
    mounted() {
        this.cargarLogs();
    }
  }
  </script>
  
  <style scoped>
  .bg-red {
    background-color: #ff6767;
  }
  
  .bg-green {
    background-color: #78ff78;
  }
  </style>
  