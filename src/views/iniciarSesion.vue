<template>
  <div class="d-lg-flex half">
    <div class="bg order-1 order-md-2">
    </div>
    <div class="contents order-2 order-md-1">
      <div class="container">
        <div class="row align-items-center justify-content-center">
          <div class="col-md-7">
            <div class="row align-items-center justify-content-center">
              <img src="../assets/img-v2/logo.svg" alt="logo" style="width: 250px; height: 250px;">
            </div>
            <h3>Iniciar sesión en <strong>SmartLearn</strong></h3>
            <p class="mb-4" style="color: black;">Software de gestión educatica, tiene como objetivo ofrecer a una
              solución eficiente para la organización de tareas y la planificación de clases en el entorno académico.</p>
            <form action="#" method="post">
              <div class="form-group first">
                <label for="username">Usuario</label>
                <input v-model="usuario" type="text" class="form-control" placeholder="correo@gmail.com" id="username">
              </div>
              <div class="form-group last mb-3">
                <label for="password">Contraseña</label>
                <input v-model="contrasena" type="password" class="form-control" placeholder="Tu contraseña"
                  id="password">
              </div>


              <div class="d-flex justify-content-center mt-3 login_container" v-if="prueba == 0">
                <vs-button dark class="btn login_btn" @click="login">Iniciar Sesión</vs-button>
              </div>

              <v-snackbar v-model="isBusy" :timeout="2000" absolute bottom color="red">
                {{ msg }}
              </v-snackbar>
            </form>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>
  <script>
   /* data () {
        return {
          dialog: false,
        }
      },*/
  
  import axios from 'axios'
    
    export default {
      name: 'App',
      data: () => ({
        active: 'home',
        dialog: false,
        usuario:"",
        noti:null,
        contrasena:"",
        error:false,
        msg:"",
        isBusy: false,
        prueba: 0,
        array: [],
        rolMenu: []
      }),
           
        methods: {
          
     async  login() {
       // this.isBusy=true
       if(this.usuario!=""){
        if(this.contrasena!=""){
        this.prueba=1;
        let autentication = {
          "username": this.usuario,
          "password": this.contrasena,
        };
        
        await axios.post("http://localhost:3001/auth", autentication).then((data) => {
            if(data.status === 200)
            console.log(data);
            /* if(data.data.rol == "Administrador"){
            } */
            /* if(data.data.rol == "Administrador"){
              axios.get('http://localhost:3001/roles')
              .then((response) => {
              console.log(response.data)
              for (i of response.data){
                if(i.name == "ADMINISTRADOR"){
                  console.log(i.menu[0]);
                  // this.$store.commit("setMenu", i.menu[0].toString());
                }
              }           })
            this.$store.commit("setAsignaturasDocente", this.array);
          }

            if(data.data.rol == "Docente"){
              axios.get('http://localhost:3001/roles')
              .then((response) => {
              console.log(response)
                this.rolMenu=response.data;
                console.log(response.data[0].menu[0])
                })
            for(var i in data.data.asignatures){
              this.array.push(data.data.asignatures[i].name)
            }
            this.$store.commit("setAsignaturasDocente", this.array);
            console.log(this.$store.state.asignaturasDocente);
          } */

        console.log(data.data);
        this.$store.state.userData = data.data;
            this.$router.push('dashboard/welcome');
             }).catch((error)=>{
              this.error = true;
              console.log(error);
              this.msg="Usuario O Contraseña Son Incorrectos"
              this.isBusy=true
              this.prueba=0;
             });
            }else{
              this.msg="El Campo Contraseña Es Necesario"
              this.isBusy=true
             }
             }else{
              this.msg="El Campo Usuario Es Necesario"
              this.isBusy=true
             }
            }
          }
    };
  
  </script>
  <style scoped>

body {
  font-family: "Roboto", sans-serif;
  background-color: #fff;
}

p {
  color: #b3b3b3;
  font-weight: 300;
}

.bg {
  background-image: url('../assets/img-v2/bg-1.png');
}

h1,
h2,
h3,
h4,
h5,
h6,
.h1,
.h2,
.h3,
.h4,
.h5,
.h6 {
  font-family: "Roboto", sans-serif;
}

a {
  -webkit-transition: .3s all ease;
  -o-transition: .3s all ease;
  transition: .3s all ease;
}

a:hover {
  text-decoration: none !important;
}

.content {
  padding: 7rem 0;
}

h2 {
  font-size: 20px;
}

.half,
.half .container>.row {
  height: 100vh;
  min-height: 700px;
}

@media (max-width: 991.98px) {
  .half .bg {
    height: 200px;
  }
}

.half .contents {
  background: #f6f7fc;
}

.half .contents,
.half .bg {
  width: 50%;
}

@media (max-width: 1199.98px) {

  .half .contents,
  .half .bg {
    width: 100%;
  }
}

.half .contents .form-control,
.half .bg .form-control {
  border: none;
  -webkit-box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.1);
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.1);
  border-radius: 4px;
  height: 54px;
  background: #fff;
}

.half .contents .form-control:active,
.half .contents .form-control:focus,
.half .bg .form-control:active,
.half .bg .form-control:focus {
  outline: none;
  -webkit-box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.1);
  box-shadow: 0 1px 2px 0 rgba(0, 0, 0, 0.1);
}

.half .bg {
  background-size: cover;
  background-position: center;
}

.half a {
  color: #888;
  text-decoration: underline;
}

.half .btn {
  height: 54px;
  padding-left: 30px;
  padding-right: 30px;
}

.half .forgot-pass {
  position: relative;
  top: 2px;
  font-size: 14px;
}

.control {
  display: block;
  position: relative;
  padding-left: 30px;
  margin-bottom: 15px;
  cursor: pointer;
  font-size: 14px;
}

.control .caption {
  position: relative;
  top: .2rem;
  color: #888;
}

.control input {
  position: absolute;
  z-index: -1;
  opacity: 0;
}

.control__indicator {
  position: absolute;
  top: 2px;
  left: 0;
  height: 20px;
  width: 20px;
  background: #e6e6e6;
  border-radius: 4px;
}

.control--radio .control__indicator {
  border-radius: 50%;
}

.control:hover input~.control__indicator,
.control input:focus~.control__indicator {
  background: #ccc;
}

.control input:checked~.control__indicator {
  background: #fb771a;
}

.control:hover input:not([disabled]):checked~.control__indicator,
.control input:checked:focus~.control__indicator {
  background: #fb8633;
}

.control input:disabled~.control__indicator {
  background: #e6e6e6;
  opacity: 0.9;
  pointer-events: none;
}

.control__indicator:after {
  font-family: 'icomoon';
  content: '\e5ca';
  position: absolute;
  display: none;
  font-size: 16px;
  -webkit-transition: .3s all ease;
  -o-transition: .3s all ease;
  transition: .3s all ease;
}

.control input:checked~.control__indicator:after {
  display: block;
  color: #fff;
}

.control--checkbox .control__indicator:after {
  top: 50%;
  left: 50%;
  margin-top: -1px;
  -webkit-transform: translate(-50%, -50%);
  -ms-transform: translate(-50%, -50%);
  transform: translate(-50%, -50%);
}

.control--checkbox input:disabled~.control__indicator:after {
  border-color: #7b7b7b;
}

.control--checkbox input:disabled:checked~.control__indicator {
  background-color: #7e0cf5;
  opacity: .2;
}
    
    :root {
    --bd-azul: #59DCE4;
    --bd-violet: #712cf9;
    --bd-accent: #ffe484;
  }
  .icon-move{
    animation: fly 1s infinite alternate;
    transition: all 1s;
  }

  .icon-move:hover{
    rotate: calc(10deg);
  }

  @keyframes fly {
    to {
      transform: translateY(-2px);
    }
  }

  .imgsvg{
    background-image: url(../assets/PortalEducativo.svg);
    position: absolute;
    width: 100%;
    height: 100%;
    background-repeat: no-repeat;
    background-size: cover;
  }
  .background{
    background-image:url("../assets/Background.svg");
    background-size: cover;
    background-attachment: fixed;
    margin:0; 
    position: absolute;  
    width: 100%;
    height: 100%;
  }

    
    @import url("https://fonts.googleapis.com/css?family=Mystery+Quest");
    .user_card {
    height: 500px;
    width: 350px;
    margin-top: auto;
    margin-bottom: auto;
    background: #ffffffb9;
    position: relative;
    display: flex;
    justify-content: center;
    flex-direction: column;
    padding: 10px;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
    -webkit-box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
    -moz-box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
    border-radius: 5px;
    }
    
    .brand_logo_container {
    position: absolute;
    height: 170px;
    width: 170px;
    top: -75px;
    border-radius: 50%;
    padding: 10px;
    text-align: center;
    margin-top:70px;
    }
    
    .brand_logo {
    height: 150px;
    width: 150px;
    border-radius: 50%;
    border: 2px solid white;
    margin-top: 16px;
    }
    
    .form_container {
    margin-top: 100px;
    }
    
    .login_btn {
    width: 100%;
    background: #4B52D1 !important;
    color: #FFFFFF !important;
    font-weight: bold;
    
    }
    
    .login_btn:focus {
    box-shadow: none !important;
    outline: 0px !important;
    }
    
    .login_container {
    padding: 0 2rem;
    }
    
    .input-group-text {
    background: #0079C4 !important;
    color: #FFFFFF !important;
    border: 0 !important;
    border-radius: 0.25rem 0 0 0.25rem !important;
    }
    
    .input_user,
    .input_pass:focus {
    box-shadow: none !important;
    outline: 0px !important;
    }
    
    .custom-checkbox .custom-control-input:checked~.custom-control-label::before {
    background-color: #c0392b !important;
    }
  </style>