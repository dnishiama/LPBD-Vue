<template>
  <div class="card-body">
    <div class="col-lg-12" v-if="usuario">
      <router-link
        class="glyphicon glyphicon-plus btn-lg"
        to="/departamento/cadastrar"
        aria-label="Alinhar na esquerda"
        style="color:blue"
        v-if="usuario"
      >Add</router-link>
    </div>

    <div id="divListar">
      
      <table id="tabDepartamento" class="table table-striped">
        <thead>
          <tr>
            
            <th>Campus</th>
            <th>Bloco</th>
            <th>Departamento</th>
            <th>Gestor</th>
            <th>Email</th>
            <th>C. Custos</th>
            <th class="actions" v-if="usuario">Ações</th>
          </tr>
        </thead>
        <tbody>
          <tr v-bind:departamento="departamento" v-for="departamento in departamentos" :key="departamento.id">
            
            <td>{{ departamento.campus }}</td>
            <td>{{ departamento.bloco }}</td>
            <td>{{ departamento.departamento }}</td>
            <td>{{ departamento.gestor.nome }}</td>
            <td>{{ departamento.gestor.email }}</td>
            <td>{{ departamento.ccusto }}</td>
            <button
              class="glyphicon glyphicon-trash mr-1"
              type="submit"
              style="color:red"
              v-if="usuario"
              v-on:click="excluir(departamento.id)"
            ></button>
            <button
              class="glyphicon glyphicon-pencil mr-1"
              type="button"
              v-on:click="editar(departamento.id)"
              style="color:blue"
              v-if="usuario"
            ></button>            
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import { mapState } from "vuex";

export default 
{
  name: "dep",
  data() {
    return {
      id: "",
      campus: "",
      bloco: "",
      departamento: "",
      gestorNome: "",
      gestorEmail: "",
      ccusto: "",
      departamentos: []
    };
  },

  computed: {
      ...mapState([
          'usuario',
          'autorizacao'
      ])
  },

  mounted() {
    if (!this.usuario) {
      this.$router.push({ path: "/" })
    }    
  },

  async beforeMount() {
    axios
        .get("/departamento", { headers: { Accept: "application/json" } })
        .then(res => { console.log(res);
          this.departamentos = res.data;
        })
        .catch(error => console.log(error));
  },
  
  methods: {
    excluir(id) {
      var resposta = confirm("Deseja remover esse registro?");
        if (resposta == true) {
        axios
          .delete("/departamento/deletar/" + id)
          .then(res => { console.log(res);
            alert("Departamento removido com sucesso!!!");
            this.$router.go(0);
          })
          .catch(error => console.log(error));
      }      
    },

    editar(departamentoid) {
      this.$router.push("/departamento/atualizar/" + departamentoid);
    }
  }
};
</script>