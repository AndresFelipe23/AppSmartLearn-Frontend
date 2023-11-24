<template>
  <div class="noticias-lista">
    <div class="filtro" v-if="noticias.length > 0">
      <input v-model="busqueda" type="text" class="v-input" placeholder="Buscar noticias" @input="realizarBusqueda">
      <select v-model="filtroCategoria" class="v-select categoria-select" @change="busquedaCategoria">
        <option value="">Todas las categorías</option>
        <option v-for="(categoria, index) in categorias" :key="index">{{ categoria }}</option>
      </select>
      <input v-model="filtroFecha" type="date" class="v-input" @change="busquedaFecha">
    </div>
    <br>
    <div>
      <div class="tabla-responsive">
        <table v-if="noticias.length > 0">
          <thead>
            <tr>
              <th>Autor</th>
              <th>Categoría de la noticia</th>
              <th>Fecha de publicación</th>
              <th>Estado de la noticia</th>
              <th>Visualizar</th>
              <th>Comentarios</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(noticia, index) in noticias" :key="index">
              <td>{{ noticia.autor }}</td>
              <td>{{ noticia.category }}</td>
              <td>{{ noticia.time }}</td>
              <td>{{ getStatus(noticia.estado) }}</td>
              <td>
              <v-btn @click="abrirModal2(index)" color="secondary">
                <b-icon icon="Eye-fill" font-scale="2" style="color:rgb(255, 255, 255)"></b-icon>
            </v-btn>
            </td>
              <td>
                <v-btn color="secondary" v-if="noticia.estado === '6502fc246f68e90bf2ac9222'" @click="abrirModal(index)"
                  class="boton-editar">
                  <b-icon icon="Eye-fill" font-scale="2" style="color:rgb(255, 255, 255)"></b-icon> Comentarios </v-btn>
                  <h6 v-if="noticia.estado !== '6502fc246f68e90bf2ac9222'">Sin comentarios</h6>
                </td>
            </tr>
          </tbody>
        </table>  
        <div v-if="noticias.length == 0">
      <h2><strong>No has creado noticias</strong></h2>
      </div>
      </div>
      <vs-dialog v-if="modalActivo" not-center v-model="modalActivo" class="dialog">
        <template #header>
        </template>
        <template #default>
          <div class="con-content">
            {{ noticiaSeleccionada.comentario }}
          </div>
        </template>
        <template #footer>
          <div class="con-footer d-flex justify-content-end">
            <router-link :to="'/dashboard/welcome/creanotice/' + noticiaSeleccionada._id"> <vs-button class="mr-2"
                color="#00BA00">Editar noticia</vs-button></router-link>
            <vs-button color="#FF3232" @click="deleteNotice(noticiaSeleccionada._id)">Cancelar noticia</vs-button>
          </div>
        </template>

      </vs-dialog>

      <vs-dialog v-if="modalActivo2" not-center v-model="modalActivo2" class="dialog">
      <template #header>
        <strong>
          <h4 class="not-margin">{{ noticiaSeleccionada.title }}</h4>
        </strong>
      </template>
      <template #default>
        <div class="con-content">
          <table class="table">
            <tr>
              <td><strong>Autor:</strong></td>
              <td>{{ noticiaSeleccionada.autor }}</td>
            </tr>
            <tr>
              <td><strong>Categoría:</strong></td>
              <td>{{ noticiaSeleccionada.category }}</td>
            </tr>
            <tr>
              <td><strong>Fecha de Publicación:</strong></td>
              <td>{{ noticiaSeleccionada.time }}</td>
            </tr>
            <tr>
              <td><strong>Resumen:</strong></td>
              <td v-html="processedAbstract"></td>
            </tr>
            <tr>
              <td><strong>Contenido:</strong></td>
              <td v-html="processedContent"></td>
            </tr>
            <tr>
              <td><strong>Imágen:</strong></td>
              <td>
                <v-img style="cursor: pointer;" height="150px" :src="noticiaSeleccionada.images"
                  @click="abrirModalImagen"></v-img>
              </td>
            </tr>
          </table>
        </div>
      </template>
      <template #footer>
        <div class="con-footer d-flex justify-content-end">
          <vs-button class="mr-2" color="#00BA00" @click="modalActivo = false">Cerrar</vs-button>
        </div>
      </template>

    </vs-dialog>

      <vs-dialog v-if="modalImage" not-center v-model="modalImage" class="dialog">
        <template #default>
          <div class="con-content">
            <v-img height="auto" :src="image"></v-img>
          </div> 
        </template>

      </vs-dialog>
      <transition name="modal">
        <vs-dialog v-if="showModal" v-model="showModal" hide-header-close>
          <div class="con-content">
            {{ mensaje }}
          </div>
        </vs-dialog>
      </transition>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      busqueda: '',
      filtroCategoria: '',
      filtroFecha: '',
      noticias: [],
      noticiasporDefecto: [],
      categorias: [],
      idNoticia: '',
      modalActivo: false,
      modalActivo2: false,
      modalImage: false,
      noticiaSeleccionada: null,
      image: null,
      currentIndex: null,
      mensaje: '',
      showModal: false,
      items: [
          { age: 40, first_name: 'Dickerson', last_name: 'Macdonald' },
          { age: 21, first_name: 'Larsen', last_name: 'Shaw' },
          { age: 89, first_name: 'Geneva', last_name: 'Wilson' },
          { age: 38, first_name: 'Jami', last_name: 'Carney' }
        ]
    };
  },
  mounted() {
    this.getNotices()
    this.userid = this.$store.state.userData._id
    console.log('userid es', this.userid)



  },
  computed: {
    processedAbstract() {
      return this.noticiaSeleccionada.abstract.replace(/<strong>/g, '<b>').replace(/<\/strong>/g, '</b>');
    },
    processedContent() {
      return this.noticiaSeleccionada.content.replace(/<strong>/g, '<b>').replace(/<\/strong>/g, '</b>');
    }
  },

  methods: {
    getNotices() {
      axios
        .get('http://localhost:3001/notices')
        .then((response) => {
          this.noticias = response.data.filter(noticia => noticia.idAutor === this.$store.state.userData._id);
          this.noticiasporDefecto = [...response.data];
          this.categorias = this.obtenerCategorias(response.data); // Obtener categorías únicas
          console.log(this.noticias);
        })
        .catch((error) => {
          console.error('Error al obtener noticias', error);
        });
    },


    getStatus(estado) {
      console.log('estado', estado)

      switch (estado) {
        case "6502fbb06f68e90bf2ac9220":
          return 'Redactado';
        case "6502fc146f68e90bf2ac9221":
          return 'Publicado';
        case "6502fc246f68e90bf2ac9222":
          return 'Rechazada';
        default:
          return 'Estado de noticia desconocido';
      }
    },

    abrirModal(index) {
      this.noticiaSeleccionada = this.noticias[index]
      this.currentIndex = index;
      console.log('noticiaSeleccionada', this.noticiaSeleccionada)
      console.log('currentIndex', this.currentIndex)
      this.modalActivo = true
    },
    abrirModa2(index) {
      this.noticiaSeleccionada = this.noticias[index]
      this.currentIndex = index; 
      console.log('noticiaSeleccionada', this.noticiaSeleccionada)
      console.log('currentIndex', this.currentIndex)
      this.modalActivo2 = true
    },

    abrirModalImagen() {
      this.image = this.noticiaSeleccionada.images;
      this.modalImage = true;
    },

    editar() {
      if (this.currentIndex !== null) {
        this.idNoticia = this.noticias[this.currentIndex]._id
        console.log(this.currentIndex)
        console.log(this.idNoticia)

        axios.put(`http://localhost:3001/noticias/${this.idNoticia}`, {
          aprobada: true
        })
          .then(response => {
            console.log('noticia aprobada', response)
            this.mensaje = 'Noticia aprobada'
            this.showModal = true

            setTimeout(() => {
              this.showModal = false;
              this.mensaje = '';
            }, 3000);
          })
          .catch(error => {
            console.error(error);
          })
      }
    },

    deleteNotice(id) {
      const idUser = id
      console.log('id', idUser)
      axios.delete(`http://localhost:3001/deletenotices/${idUser}`)
        .then(response => {
          console.log(response.data);
          console.log('noticia eliminada', response)
            this.mensaje = 'Noticia eliminada'
            this.showModal = true

            setTimeout(() => {
              this.showModal = false;
              this.mensaje = '';
            }, 3000);
            this.getNotices()
            this.modalActivo = false
        })
        .catch(error => {
          console.log(error);
        });
    },

    // 

    realizarBusqueda() {
      if (this.busqueda) {
        this.noticias = this.noticiasporDefecto.filter(noticia =>
          Object.values(noticia).some(valor =>
            String(valor).toLowerCase().includes(this.busqueda.toLowerCase())
          )
        );
      } else {
        this.noticias = [...this.noticiasporDefecto];
      }
    },
    busquedaCategoria() {
      if (this.filtroCategoria) {
        this.noticias = this.noticiasporDefecto.filter((noticia) =>
          noticia.category.toLowerCase().includes(this.filtroCategoria.toLowerCase())
        );
      } else {
        this.noticias = [...this.noticiasporDefecto];
      }
    },
    busquedaFecha() {
      if (this.filtroFecha) {
        this.noticias = this.noticiasporDefecto.filter((noticia) =>
          noticia.fechaPublicacion.includes(this.filtroFecha)
        );
      } else {
        this.noticias = [...this.noticiasporDefecto];
      }
    },
    obtenerCategorias(noticias) {
      // Función para obtener categorías únicas de las noticias
      const categoriasUnicas = new Set();
      noticias.forEach((noticia) => {
        categoriasUnicas.add(noticia.category);
      });
      return Array.from(categoriasUnicas);
    },
  },
};
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}


.dialog {
  width: 100%;

}

.con-content td {
  overflow: auto;
}

th,
td {
  border: 1px solid #ccc;
  text-align: center;
  padding: 10px;
}

th {
  background-color: #4c38ff;
  /* Cambia el color de fondo del encabezado */
  color: white;
  /* Cambia el color del texto del encabezado */
}

tr:nth-child(even) {
  background-color: #f2f2f2;
}

.modal-enter-active,
.modal-leave-active {
  transition: opacity 0.3s;
}

.modal-enter,
.modal-leave-to {
  opacity: 0;
}

.v-input,
.v-select {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-right: 10px;
}

.v-input[type="date"] {
  padding: 6px;
}

.v-select {
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 4px;
  margin-right: 10px;
  width: 200px;

}

.categoria-select {
  background-color: #fff;
  border: 1px solid #ccc;
  border-radius: 4px;
  padding: 8px;
  width: 12%;
}

.noticias-lista {
  text-align: center;
  padding: 20px;
}

.filtro {
  display: flex;
  justify-content: center;
}

.filtro input,
select {
  margin-right: 40px;
  margin-left: 40px;

}

.boton-editar,
.boton-borrar {
  background-color: green;
  color: white;
  border: none;
  padding: 5px 10px;
  margin: 2px;
  cursor: pointer;
  border-radius: 5px;
  font-size: 14px;
}

.boton-editar:hover,
.boton-borrar:hover {
  background-color: darkgreen;
}

.boton-borrar {
  background-color: red;
}

.boton-borrar:hover {
  background-color: darkred;
}</style>