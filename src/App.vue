<template>
  <div id="app">
    <div
      class="modal fade"
      id="exampleModal"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-body">
            <table>
              <tr>
                <td>Nombre</td>
                <td><input v-model="nameEdit" type="text" /></td>
              </tr>
              <tr>
                <td>Apellido</td>
                <td><input v-model="lastnameEdit" type="text" /></td>
              </tr>
              <button @click="update">Editar</button>
            </table>
          </div>
        </div>
      </div>
    </div>

    <table>
      <tr>
        <td>Nombre</td>
        <td><input v-model="name" type="text" /></td>
      </tr>
      <tr>
        <td>Apellido</td>
        <td><input v-model="lastname" type="text" /></td>
      </tr>
      <button @click="create">Agregar</button>
    </table>

    <table class="table">
      <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">Nombre</th>
          <th scope="col">Apellido</th>
          <th scope="col">Acción</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(user, i) in users" :key="i">
          <th scope="row">{{ i + 1 }}</th>
          <td>{{ user.data.name }}</td>
          <td>{{ user.data.lastname }}</td>
          <td>
            <button
              data-toggle="modal"
              data-target="#exampleModal"
              class="btn btn-warning"
               @click="setInformation(user.data.name, user.data.lastname, user.id)"
            >
              Editar</button
            ><button @click="Delete(user.id)" class="btn btn-danger">
              Eliminar
            </button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: white;
  background-color: blueviolet;
}

#nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>

<script>
import firebase from "firebase";
export default {
  data() {
    return {
      loading: true,
      users: [],
      name: "",
      lastname: "",
      nameEdit: '',
      lastnameEdit: '',
      idEdit:''
    };
  },
  mounted() {
    this.read();
    // this.create()
    // this.update()
    // this.delete();
  },
  methods: {
    setInformation(name, lastname, id){
      this.nameEdit = name
      this.lastnameEdit = lastname
      this.idEdit = id
    },

    read() {
      firebase
        .firestore()
        .collection("users")
        .onSnapshot((snapshot) => {
          this.users = [];
          snapshot.forEach((doc) => {
            console.log(doc.id, "=>", doc.data());
            this.users.push({
              id: doc.id,
              data: doc.data(),
            });
          });
        });
    },
    create() {
      firebase
        .firestore()
        .collection("users")
        .add({
          name: this.name,
          lastname: this.lastname,
        })
        .then(() => {
          console.log("Data enviada");
        });
    },
    update() {
      firebase
        .firestore()
        .collection("users")
        .doc(this.idEdit)
        .update({
          name: this.nameEdit,
          lastname: this.lastnameEdit,
        })
        .then(() => {
          console.log("Data enviada");
        });
    },
    Delete(id) {
      firebase
        .firestore()
        .collection("users")
        .doc(id)
        .delete();
    },
  },
};
</script>
