<template>
    <div>

        <v-container class="body-component">
            <!-- Page title-->
            <div class="border-bottom pt-5 mb-5">
                <h1 class="mt-2 mt-md-4 mb-3 pt-5">Crear noticias</h1>
                <div class="d-flex flex-wrap flex-md-nowrap justify-content-between">
                    <p class="text-muted">Este módulo crea las noticias.</p>

                </div>
            </div>

            <div class="card box-shadow-sm">
                <div class="card-header">
                    <h5 style="margin-bottom: 0px;">Registro de noticias</h5>
                </div>
                <div class="card-body">
                    <div class="row">
                        <div class="col-lg-8">
                            <div class="row">
                                <div class="col-lg-12 form-group">
                                    <label for="">Titulo de la noticia</label>
                                    <input type="text" v-model="noticeFile.title" class="form-control"
                                        placeholder="Titulo de la noticia" required>
                                </div>
                                <div class="col-lg-4 form-group">
                                    <label for="">Nombre del </label>
                                    <input type="text" v-model="noticeFile.autor" class="form-control" placeholder="Autor"
                                        required>
                                </div>

                                <div class="col-lg-4 form-group">
                                    <label for="">Categoría</label>
                                    <v-select :items="items" v-model="noticeFile.category" menu-props="auto"
                                        label="Categoria" hide-details single-line variant="outlined"></v-select>

                                </div>


                                <div class="col-lg-12 form-group">
                                    <label for="">Descripción corta</label>
                                    <textarea v-model="noticeFile.abstract" :rules="rulesAbstract" auto-grow
                                        class="form-control" placeholder="Titulo de producto" required rows="5"></textarea>
                                </div>

                                <div class="col-lg-12 form-group">
                                    <vue-editor id="editor" v-model="noticeFile.content" useCustomImageHandler
                                        @blur="extractTextFromContent">
                                        <!--@image-added="handleImageAdded"-->
                                    </vue-editor>
                                </div>

                            </div>
                        </div>
                        <div class="col-lg-4">
                            <div class="row">
                                <div class="col-lg-12 form-group">
                                    <label for="">Imagen</label>
                                    <div class="custom-file">
                                        <v-file-input v-model="noticeFile.images" label="Imagen de la noticia"
                                            variant="filled" accept="image/png, image/jpeg, image/bmp"
                                            prepend-icon="mdi-camera"></v-file-input>
                                    </div>
                                </div>
                                <div class="col-lg-12">
                                    <img src="path-to-image" class="img-thumbnail" alt="Rounded image">
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
                <div class="card-footer">
                    <button class="btn" style="background: #302387; color: #fff" @click="submitForm">Crear noticia</button>
                </div>

            </div>

        </v-container>
    </div>
</template>

<script>
import { VueEditor } from 'vue2-editor';
import axios from 'axios';
import console from 'console';
//const { ObjectId } = require('mongodb')
const mongoose = require('mongoose')


export default {
    components: {
        VueEditor
    },
    
    data: () => ({
        prueba:true,
        snackbar: false,
        text: 'Noticia guardada correctamente... :D',
        timeout: 2000,
        loading: false,
        noticeFile: {
            title: "",
            autor: "",
            category: "",
            time: null,
            abstract:"",
            content:"",
            images:"",
        },
        getNoticesFile:[],
        latestNoticeFromData: null,
        items: ['Educacion', 'Deporte', 'Administrativo', 'Politica'],
        rulesAbstract: [v => v.length <= 500 || 'Maximo 500 Palabras'],
        titleRules: [v => !!v || 'El Título es requerido'],
        authorRules: [v => !!v || 'El Autor es requerido'],
        paquete: null,
        isEditing: false

    }),

    mounted() {
        const options = { year: 'numeric', month: 'long', day: 'numeric', hour: 'numeric', minute: 'numeric' };
        this.noticeFile.time = new Date().toLocaleString(undefined, options);
        this.noticeFile.autor = this.$store.state.userData.name 

    },

    computed: {
        formIsValid() {
        // Verificar si todos los campos requeridos están completos
            return this.noticeFile.title && this.noticeFile.autor&& this.noticeFile.category&& this.noticeFile.time
            && this.noticeFile.abstract && this.noticeFile.content
            && this.noticeFile.images&& this.prueba==true;
        },
    },
    created() {
    this.capturarNoticias()
    const noticiaId = this.$route.params._id;
    if (noticiaId) {

        this.isEditing = true;
      this.cargarNoticiaParaEditar(noticiaId);
    }
  },

   
    methods: {

        clearForm() {
                this.noticeFile.title = '';
                this.noticeFile.autor = '';
                this.noticeFile.category = '';
                this.noticeFile.abstract = '';
                this.noticeFile.content = '';
                this.noticeFile.images = '';
            },

        extractTextFromContent() {
            const tempEl = document.createElement('div');
            tempEl.innerHTML = this.noticeFile.content;

            this.noticeFile.abstract = tempEl.textContent || tempEl.innerText;
        },

       /* handleImageAdded: function(file, Editor, cursorLocation, resetUploader) {
            var formData = new FormData();
            formData.append("image", file);

            axios({
                url: "http://localhost:3001/images",
                method: "POST",
                data: formData
            })
                .then(result => {
                const url = result.data.url;
                this.noticeFile.images = url
                Editor.insertEmbed(cursorLocation, "image", url);
                resetUploader();
                })
                .catch(err => {
                console.log(err);
                });
            }, */

     //Tomar los datos de la noticia y enviarlo al Backend   
    async submitForm() {
        if(this.isEditing) {
        return
        }
        this.prueba=false
        const formData = new FormData();
       formData.append('file',this.noticeFile.images) 
       await axios.post('http://localhost:3001/imagenNoticia', formData).then(async response => {
        console.log(response.data.status)
        if(response.data.status==200){
            let paquete = {
            title: this.noticeFile.title, 
            autor: this.noticeFile.autor,
            idAutor: this.$store.state.userData._id,
            category: this.noticeFile.category,
            time: this.noticeFile.time,
            abstract: this.noticeFile.abstract,
            content: this.noticeFile.content,
           images:response.data.nU
        }
        await axios.post('http://localhost:3001/notice', paquete).then(response => {
            this.snackbar = true;
            if(response.status =="200"){
                console.log(response.data)}
                this.clearForm()
                this.prueba=true
        })
        .catch(error => {
            // Aquí puedes manejar los errores de la petición
            console.error(error);
        });
        }
       })
        
        
    },
    async capturarNoticias() {
    const noticiaId = this.$route.params._id;
    console.log('noticia obtenida', noticiaId);
        await axios.get(`http://localhost:3001/notices/${noticiaId}`)
        .then(response => {
            const datosNoticia = response.data; 
            this.noticeFile.title = datosNoticia.title;
            this.noticeFile.autor = datosNoticia.autor;
            this.noticeFile.category = datosNoticia.category;
            this.noticeFile.time = datosNoticia.time;
            this.noticeFile.abstract = datosNoticia.abstract;
            this.noticeFile.content = datosNoticia.content;
            // this.noticeFile.images = datosNoticia.images
            console.log(response)

    })
    console.log('noticeFile1', this.noticeFile);

    this.paquete = {
                    title: this.noticeFile.title, 
                    autor: this.noticeFile.autor,
                    idAutor: this.$store.state.userData._id,
                    category: this.noticeFile.category,
                    time: this.noticeFile.time,
                    abstract: this.noticeFile.abstract,
                    content: this.noticeFile.content,
                    images: null

                };
    },


//     async EditarNoticias() {
//   const noticiaId = this.$route.params._id; 
//   console.log('noticia obtenida', noticiaId);

//   try {
//     if (this.noticeFile.images && this.noticeFile.images.length > 0) { 
//       const formData = new FormData();
//       formData.append('file', this.noticeFile.images); 
//       const updateImage = await axios.post('http://localhost:3001/imagenNoticiaNueva', formData);

//       if (updateImage.data.status === 200) {
//         const newUrl = updateImage.data.nU;
//         console.log('url', newUrl);
//         const updatedNotice = {
//           title: this.noticeFile.title, 
//           autor: this.noticeFile.autor,
//           idAutor: this.$store.state.userData._id,
//           category: this.noticeFile.category,
//           time: this.noticeFile.time,
//           abstract: this.noticeFile.abstract,
//           content: this.noticeFile.content,
//           images: newUrl,
//           estado: new mongoose.Types.ObjectId('6502fbb06f68e90bf2ac9220')  
//         };

//         console.log('updatedNotice', updatedNotice);
//         const updateResponse = await axios.put(`http://localhost:3001/noticiasn/${noticiaId}`, updatedNotice);

//         if (updateResponse.status === 200) {
//           console.log(updateResponse.data);
//           this.clearForm();
//         }
//       }
//     } else {
//       const updatedNotice = {
//         title: this.noticeFile.title, 
//         autor: this.noticeFile.autor,
//         idAutor: this.$store.state.userData._id,
//         category: this.noticeFile.category,
//         time: this.noticeFile.time,
//         abstract: this.noticeFile.abstract,
//         content: this.noticeFile.content,
//         estado: new mongoose.Types.ObjectId('6502fbb06f68e90bf2ac9220')  
//       };

//       console.log('updatedNotice', updatedNotice);
//       const updateResponse = await axios.put(`http://localhost:3001/noticiasn/${noticiaId}`, updatedNotice);

//       if (updateResponse.status === 200) {
//         console.log(updateResponse.data);
//         this.clearForm();
//       }
//     }
//   } catch (error) {
//     // Manejar errores en la solicitud
//     console.error('Error al actualizar la noticia', error);
//   }
// }

async EditarNoticias() {
  const noticiaId = this.$route.params._id;
  console.log('noticia obtenida', noticiaId);

  try {
    const updatedNotice = {
      idAutor: this.$store.state.userData._id,
      estado: new mongoose.Types.ObjectId('6502fbb06f68e90bf2ac9220'),
    };

    // Agregar campos al objeto solo si tienen contenido
    if (this.noticeFile.title) updatedNotice.title = this.noticeFile.title;
    if (this.noticeFile.autor) updatedNotice.autor = this.noticeFile.autor;
    if (this.noticeFile.category) updatedNotice.category = this.noticeFile.category;
    if (this.noticeFile.time) updatedNotice.time = this.noticeFile.time;
    if (this.noticeFile.abstract) updatedNotice.abstract = this.noticeFile.abstract;
    if (this.noticeFile.content) updatedNotice.content = this.noticeFile.content;
    console.log('file', this.noticeFile.images);
    console.log(updatedNotice)
    console.log('Antes de entrar');

    if (this.noticeFile.images) {
        console.log('entre al primer if');
      const formData = new FormData();
      formData.append('file', this.noticeFile.images);
      const updateImage = await axios.post('http://localhost:3001/imagenNoticiaNueva', formData);

      if (updateImage.data.status === 200) {
        updatedNotice.images = updateImage.data.nU;
        console.log('entre al segundo if');
        console.log(updateImage.data.nU)
      }
    }
    console.log('sali');
    
    console.log(updatedNotice)
    const updateResponse = await axios.put(`http://localhost:3001/noticiasn/${noticiaId}`, updatedNotice);

    if (updateResponse.status === 200) {
      console.log(updateResponse.data);
      this.clearForm();
    }
  } catch (error) {
    // Manejar errores en la solicitud
    console.error('Error al actualizar la noticia', error);
  }
}

    }
}



</script>
<style scoped>
.image-small {
  width: 100px;
  height: 100px;
}

.image-medium {
  width: 200px;
  height: 200px;
}

.image-large {
  width: 300px;
  height: 300px;
}

</style>