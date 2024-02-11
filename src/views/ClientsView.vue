<template>
  <main>
    <div>
      <h1>Les clients</h1>
    </div>
    <div>
      <table>
        <caption>Les clients - page {{numPage}}/10</caption> //ADD NUMBER CHANGES
        <tr>
          <th>Code</th>
          <th>Societe</th>
          <th>Contact</th>
          <th>Ville</th>
        </tr>
        <!-- Si le tableau des catégories est vide -->
        <tr v-if="data.listeClients.length === 0">
          <td colspan="4">Veuillez patienter, chargement des clients...</td>
        </tr>
        <!-- Si le tableau des catégories n'est pas vide -->
        <tr v-for="clients in data.listeClients" :key="clients.code">
          <td>{{ clients.code }}</td>
          <td>{{ clients.societe }}</td>
          <td>{{ clients.contact }}</td>
          <td>{{ clients.adresse.ville }}</td>
        </tr>
        <tr>
          <td><button @click="chargeClients">first page</button></td>
          <td><button @click="prevPage(numPage)">previous</button></td>
          <td><button @click="nextPage(numPage)">next</button></td>
          <td><button @click="goLastPage(lastPage)">last page</button></td>
        </tr>
      </table>
    </div>
  </main>
</template>

<script setup>
import { reactive, onMounted } from "vue";
import { doAjaxRequest } from "@/api";

let data = reactive({
  // La liste des catégories affichée sous forme de table
  listeClients: []
});

function showError(error) {
  console.log("Erreur : status %d", error.status)
  console.log(error.body);
  alert(error.message);
}

let  numPage=0;
let lastPage =0;

function chargeClients() {
  // Appel à l'API pour avoir la liste des catégories
  // Trié par code, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest("/api/clients?page=0&size=5")
      .then((json) => {
        numPage=0;
        lastPage = json.page.totalPages-1;
        data.listeClients = json._embedded.clients;
      })
      .catch(showError);
}

function nextPage(num) {
  // Appel à l'API pour avoir la liste des catégories
  // Trié par code, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest(`/api/clients?page=${num+1}&size=5`)
      .then((json) => {
        if (numPage!=lastPage){
          numPage+=1;
          data.listeClients = json._embedded.clients;
        }

      })
      .catch(showError);
}

function prevPage(num) {
  // Appel à l'API pour avoir la liste des catégories
  // Trié par code, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest(`/api/clients?page=${num-1}&size=5`)
      .then((json) => {
        numPage-=1;
        data.listeClients = json._embedded.clients;
      })
      .catch(showError);
}

function goLastPage(num) {
  // Appel à l'API pour avoir la liste des catégories
  // Trié par code, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest(`/api/clients?page=${num}&size=5`)
      .then((json) => {
        numPage=lastPage;
        data.listeClients = json._embedded.clients;
      })
      .catch(showError);
}


// A l'affichage du composant, on affiche la liste
onMounted(chargeClients);

</script>


<style scoped>
td,
th {
  border: 1px solid #ddd;
  padding: 8px;
}

th {
  padding-top: 12px;
  padding-bottom: 12px;
  text-align: left;
  background-color: #232623;
  color: rgb(255, 255, 255);
}
</style>
