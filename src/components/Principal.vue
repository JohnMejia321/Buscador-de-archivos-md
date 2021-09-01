<template>
  <div>
    <p align="left">
      nombre de usuario de github <input type="text" v-model="username" />
       <button type="button" @click="getRepositorys()">Enviar</button>
    </p>
    

    <table border="1">
      <thead>
        <th>#</th>
        <th>Nombre del proyecto</th>
        <th>direccion url</th>
        <th>Lenguaje de programacion del proyecto</th>
        <th>Traer archivo MD</th>
        <th>archivos MD</th>
      </thead>
      <tbody>
        <tr v-for="(item, index) in items" :key="index">
          <td>{{ index }}</td>
          <td>{{ item.name }}</td>
          <td>{{ item.html_url }}</td>
          <td>{{ item.language }}</td>
          <td>
            <button type="button" 
                    @click="getDocumentMD(item.name, index)">
              Detalle
            </button>
          </td>
          <td>
            <ul v-for="(item, index2) in itemsMD" :key="index2">
              <li v-if="position == index">
                <a :href="item.download_url">{{ item.path }}</a>
              </li>
            </ul>
          </td>
        </tr>
      </tbody>
    </table>
    
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "Principal",
  data() {
    return {
      username: "",
      items: [],
      itemsMD: [], 
      position: 0
    };
  },
  methods: {
    getRepositorys() {
      axios
        .get(`https://api.github.com/users/${this.username}/repos`)
        .then(resp => {
          this.items = resp.data;
        })
        .catch(error => {
          console.log(error);
        });
    },
    getDocumentMD(project, index){
      let data = [];
      let ext;
      this.position = index;
      axios
        .get(
          `https://api.github.com/repos/${this.username}/${project}/contents`
        )
        .then(resp => {
          data = resp.data;
          this.itemsMD = [];
          data.forEach(element => {
            ext = element.path.split(".")[1];
            if (ext == "md") {
              this.itemsMD.push(element);
            }
          });
        })
        .catch(error => {
          console.log(error);
        });
    }
  }
};
</script>
