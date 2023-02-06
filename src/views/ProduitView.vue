<template>
  <main>
    <div>
      <table>
        <caption>
          Liste des produits - page
          {{
            data.infoPage.number + 1
          }}
          /
          {{
            data.infoPage.totalPages
          }}
        </caption>
        <tr>
          <th>Nom</th>
          <th>Prix</th>
          <th>Stock</th>
          <th>Commandés</th>
        </tr>
        <!-- Si le tableau des catégories est vide -->
        <tr v-if="!data.listeProduits">
          <td colspan="4">Veuillez patienter, chargement des produits...</td>
        </tr>
        <!-- Si le tableau des produits n'est pas vide -->
        <tr v-for="produit in data.listeProduits" :key="produit.nom">
          <td>{{ produit.nom }}</td>
          <td>{{ produit.prixUnitaire }}</td>
          <td>{{ produit.unitesEnStock }}</td>
          <td>{{ produit.unitesCommandees }}</td>
        </tr>
        <td>
          <button @click="chargeProduits(data.links.first.href)">⇇</button>
        </td>
        <td>
          <button @click="chargeProduits(data.links.prev.href)">←</button>
        </td>
        <td>
          <button @click="chargeProduits(data.links.next.href)">→</button>
        </td>
        <td>
          <button @click="chargeProduits(data.links.last.href)">⇉</button>
        </td>
      </table>
    </div>
  </main>
</template>

<script setup>
import { reactive, onMounted } from "vue";
import { BACKEND, doAjaxRequest } from "../api";

let data = reactive({
  // La liste des catégories affichée sous forme de table
  listeProduits: [],
  links: {},
  infoPage: {},
});

function chargeProduits(href) {
  // Appel à l'API pour avoir la liste des produits
  // Trié par code, descendant
  // Verbe HTTP GET par défaut
  doAjaxRequest(href)
    .then((json) => {
      data.listeProduits = json._embedded.produits;
      data.links = json._links;
      data.infoPage = json.page;
    })
    .catch((error) => alert(error.message));
}

// A l'affichage du composant, on affiche la liste
onMounted(() => chargeProduits(BACKEND + "/api/produits?page=0&size=5"));
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
