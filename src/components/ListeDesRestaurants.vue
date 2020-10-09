<template>
  <div>
   <h2>{{msg}}</h2>
    <md-dialog :md-active.sync="showDialog">
		<md-dialog-title>Ajouter un restaurant</md-dialog-title>
			<form novalidate class="md-layout" @submit.prevent="ajouterRestaurant($event)">
				<md-card class="md-layout-item md-size-100 md-small-size-100">
					<md-card-content>
						<div class="md-layout md-gutter">
							<div class="md-layout-item md-small-size-100">
								<md-field>
									<label for="first-name">Nom</label>
									<md-input name="nom" type="text" required v-model="nom"/>
								</md-field>
							</div>
						</div>
						<div class="md-layout md-gutter">
							<div class="md-layout-item md-small-size-100">
								<md-field>
									<label for="first-name">Cuisine</label>
									<md-input name="cuisine" type="text" required v-model="cuisine"/>
								</md-field>
							</div>
						</div>
					</md-card-content>

					<md-card-actions>
						<md-button class="md-primary" @click="showDialog = false">Fermer</md-button>
						<md-button type="submit" class="md-primary" @click="showDialog = false">Ajouter</md-button>
					</md-card-actions>
				</md-card>

			</form>
    </md-dialog>

    <md-button class="md-primary md-raised" @click="showDialog = true">Ajouter un restaurant</md-button>
    <md-snackbar :md-active.sync="restaurantSauvegarder">Le restaurant est enregistré !</md-snackbar>

    <h1>Nombre de restaurants : {{nbRestaurantsTotal}}</h1>
    <p>Chercher par nom : <input 
                            @input="chercherRestaurants()" 
                            type="text" 
                            v-model="nomRestauRecherche"
                          ></p>
    <p>Nb de pages total : {{nbPagesTotal}}</p>
    <p>Nb restaurants par page : 
        <input 
            @input="getRestaurantsFromServer()"
            type="range" min=2 max=1000 v-model="pagesize"
        >{{pagesize}}</p>
    <md-button :disabled="page===0" @click="pagePrecedente()">Précédent</md-button>&nbsp;&nbsp;
    <md-button :disabled="page===nbPagesTotal" @click="pageSuivante()">Suivant</md-button>
    &nbsp; Page courante : {{page}}
    <br>
    <md-table>
		<md-table-row>
			<md-table-head>Nom</md-table-head>
			<md-table-head>Cuisine </md-table-head>
		</md-table-row>
		<md-table-row 
			v-for="(r,index) in restaurants"
			:key="index"
			@click="supprimerRestaurant(r)" 
			:style="{backgroundColor:getColor(index)}"
			:class="{bordureRouge:(index === 2)}"
		>
			<md-table-cell>{{r.name}}</md-table-cell>
			<md-table-cell> {{r.cuisine}}</md-table-cell>
		</md-table-row>
	</md-table>
  </div>
</template>

<script>
import _ from "lodash";

export default {
	name: 'ListeDesRestaurants',
	data: function() {
		return {
			restaurants: [],
			nom: "",
			cuisine: "",
			nbRestaurantsTotal: 0,
			page: 0,
			pagesize: 10,
			nbPagesTotal: 0,
			msg: "",
			nomRestauRecherche: "",
			showDialog: false,
			restaurantSauvegarder: false,
		}
	},
	mounted() {
		console.log("AVANT AFFICHAGE");
		this.getRestaurantsFromServer();
	},
	methods: {
		pagePrecedente() {
			if (this.page === 0) return;

			this.page--;
			this.getRestaurantsFromServer();
		},
		pageSuivante() {
			if (this.page === this.dernierePage) return;
			this.page++;
			this.getRestaurantsFromServer();
		},
		getRestaurantsFromServer() {
			let url = "http://localhost:8080/api/restaurants?";
			url += "page=" + this.page;
			url += "&pagesize=" + this.pagesize;
			url += "&name=" + this.nomRestauRecherche;

		fetch(url)
			.then((responseJSON) => {
				// arrow functions, conserve le bon "this"
				// la réponse est en JSON, on la convertit avec la ligne suivante
				responseJSON.json().then((resJS) => {
					// Maintenant resJS est un vrai objet JavaScript
					this.restaurants = resJS.data;
					this.nbRestaurantsTotal = resJS.count;
					this.nbPagesTotal = Math.round(
						this.nbRestaurantsTotal / this.pagesize
					);
				});
			})
			.catch(function (err) {
				console.log(err);
			});
		},
		chercherRestaurants: _.debounce(function () {
			// appelée que si on n'a pas tapé de touches pendant un certain délai
			this.getRestaurantsFromServer();
		}, 300),
		supprimerRestaurant(r) {
			let url = "http://localhost:8080/api/restaurants/" + r._id;

			fetch(url, {
				method: "DELETE",
			})
			.then((responseJSON) => {
				responseJSON.json().then((resJS) => {
					// Maintenant res est un vrai objet JavaScript
					console.log(resJS.msg);
					this.msg = resJS.msg;
					// On rafraichit la vue
					this.getRestaurantsFromServer();
				});
			})
			.catch(function (err) {
				console.log(err);
			});
		},
		ajouterRestaurant(event) {
			// Récupération du formulaire. Pas besoin de document.querySelector
			// ou document.getElementById puisque c'est le formulaire qui a généré
			// l'événement
			let form = event.target;

			// Récupération des valeurs des champs du formulaire
			// en prévision d'un envoi multipart en ajax/fetch
			let donneesFormulaire = new FormData(form);

			let url = "http://localhost:8080/api/restaurants";

			fetch(url, {
				method: "POST",
				body: donneesFormulaire,
			})
			.then((responseJSON) => {
				responseJSON.json().then((resJS) => {
					// Maintenant res est un vrai objet JavaScript
					console.log(resJS.msg);
					this.msg = resJS.msg;
					// On rafraichit la vue
					this.getRestaurantsFromServer();
				});
			})
			.catch(function (err) {
				console.log(err);
			});

			window.setTimeout(() => {
				this.restaurantSauvegarder = true
			}, 1500)

			this.nom = "";
			this.cuisine = "";
			this.restaurantSauvegarder = false;
		},
		getColor(index) {
			return index % 2 ? "lightBlue" : "pink";
		},
	},
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
	small {
		display: block;
	}
	.md-dialog /deep/.md-dialog-container {
		max-width: 768px;
		padding: 25px;
	}
</style>
