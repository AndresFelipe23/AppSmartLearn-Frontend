<template>
  <v-navigation-drawer v-model="drawer" app class="fon" width="320px">
    <!-- Cambia "primary" por el color que desees -->
    <vuescroll :ops="ops">
      <v-img class="mt-2" style="margin-top: 50px;" contain height="120px" src="../../assets/img-v2/logo-3.svg"></v-img>
      <!-- <div style="text-align: center;">
        <label class="font-weight-bold letra text-h5 mx-2" for="">AULAS INTELIGENTES</label>
      </div> -->
      <v-divider></v-divider>
      <div class="app-sidebar-content">
        <sidebar-menu showOneChild :menu="menu" />
      </div>
    </vuescroll>
  </v-navigation-drawer>
</template>

<script>
import { SidebarMenu } from 'vue-sidebar-menu'
import vuescroll from "vuescroll";


export default {
  components: {
    SidebarMenu,
    vuescroll
  },

  props: ['drawer'],
  data: () => ({

    ops: {
      scrollPanel: {
        initialScrollY: false,
        initialScrollX: false,
        scrollingX: false,
        scrollingY: true,
        speed: 300,
        easing: undefined,
        verticalNativeBarPos: 'right'
      },
      rail: {

        background: 'rgba(0, 0, 0, 0.158)',
        opacity: 1,
        size: '10px',
        specifyBorderRadius: false,
        gutterOfEnds: null,
        gutterOfSide: '2px',
        keepShow: false
      },
      bar: {
        showDelay: 500,
        onlyShowBarOnScroll: true,
        keepShow: false,
        background: '#0378a677',
        opacity: 1,
        hoverStyle: false,
        specifyBorderRadius: false,
        minSize: 0,
        size: '9px',
        disable: false,
        userData: null
      },
    },

    links: [
      ['mdi-inbox-arrow-down', 'Inbox'],
      ['mdi-send', 'Send'],
      ['mdi-delete', 'Trash'],
      ['mdi-alert-octagon', 'Spam'],
    ],

    menu: [],
  }),
  mounted() {
    const rolEspecifico = "6525b7b49b07590f3ba23626";
    const isEditor = this.$store.state.userData.isEditor;
    const isRedactor = this.$store.state.userData.isRedactor;
    


    this.menu = this.$store.state.userData.menu;

    console.log("rol", this.$store.state.userData.rol);
    console.log("editor", this.$store.state.userData.isEditor);
    console.log("redactor", this.$store.state.userData.isRedactor);

    if (this.$store.state.userData.rol.includes(rolEspecifico)) {
      if (isEditor && isRedactor) {
        this.menu = this.$store.state.userData.menu;
      } else {
        if (!isEditor && !isRedactor) {
          this.menu = this.menu.filter(item => item.title !== 'Noticias')
        } else {
          if (isEditor) {
            this.menu[2].child = this.menu[2].child.filter(item => item.title !== 'Crear' && item.title !== 'Listar');
          }

          if (isRedactor) {
            this.menu[2].child = this.menu[2].child.filter(item => item.title !== 'Editar');

          }
        }
      }
    } else {
      this.menu = this.$store.state.userData.menu;
    }
  }


}
</script>
<style scoped>
.fon {
  /* background-image:url("../../assets/images/sidebar/abstract4.jpg");*/
  background-image: url("../../assets/fondosidebarv7-2.svg");
  background-size: cover;
  background-attachment: fixed;
  width: 100%;
  background-position: center;
  height: 100%;
  margin: 0;

}

.letra {
  color: #FFFFFF;
  font-size: 20px;
  /* font-family: "Mystery Quest";*/
  font-weight: 400;

}
</style>