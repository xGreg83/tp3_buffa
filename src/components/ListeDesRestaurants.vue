<template>
  <div class="page-container">
	<md-app>
		<md-app-content>
			<md-dialog :md-active.sync="showAddForm">
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
								<md-button class="md-primary" @click="showAddForm = false">Fermer</md-button>
								<md-button type="submit" class="md-primary" @click="showAddForm = false">Ajouter</md-button>
							</md-card-actions>
						</md-card>

					</form>
			</md-dialog>

			<md-dialog :md-active.sync="showEditForm">
				<md-dialog-title>Modifier un restaurant</md-dialog-title>
					<form novalidate class="md-layout" @submit.prevent="modifierRestaurant($event)">
						<md-card class="md-layout-item md-size-100 md-small-size-100">
							<md-card-content>
								<div class="md-layout md-gutter">
									<div class="md-layout-item md-small-size-100">
										<md-field>
											<label>Nom</label>
											<md-input name="nom" type="text" required v-model="editnom"/>
										</md-field>
									</div>
								</div>
								<div class="md-layout md-gutter">
									<div class="md-layout-item md-small-size-100">
										<md-field>
											<label>Cuisine</label>
											<md-input name="cuisine" type="text" required v-model="editcuisine"/>
										</md-field>
									</div>
								</div>
							</md-card-content>

							<md-card-actions>
								<md-button class="md-primary" @click="showEditForm = false">Fermer</md-button>
								<md-button type="submit" class="md-primary" @click="showEditForm = false">Modifier</md-button>
							</md-card-actions>
						</md-card>

					</form>
			</md-dialog>

			<md-dialog :md-active.sync="showDetailsForm">
				<md-dialog-title>Informations</md-dialog-title>

				<md-tabs md-dynamic-height>
					<md-tab md-label="Détails du restaurant">
						<ul>
							<li>Nom du restaurant : {{nom}}</li>
							<li>Type de cuisine : {{cuisine}}</li>
						</ul>
					</md-tab>

					<md-tab md-label="Notation">
						<md-table>
							<md-table-row>
								<md-table-head></md-table-head>
								<md-table-head>Date</md-table-head>
								<md-table-head>Grade</md-table-head>
								<md-table-head>Note</md-table-head>
							</md-table-row>

							<md-table-row v-for="item in grades" :key="item.date">
								<md-table-cell><span class="material-icons">grade</span></md-table-cell>
								<md-table-cell>{{ format_date(item.date) }}</md-table-cell>
								<md-table-cell>{{ item.grade }}</md-table-cell>
								<md-table-cell>{{ item.score }}</md-table-cell>
							</md-table-row>
						</md-table>
					</md-tab>

					<md-tab md-label="Emplacement">
						Ville : {{ville}}
						<br>
						Adresse complète : {{adresse.building}} {{adresse.street}}, {{ville}} {{adresse.zipcode}}
						<br>
						<div style="height: 500px; width: 100%">
							<l-map
							:zoom="zoom"
							:center="center"
							:options="mapOptions"
							style="height: 80%"
							@update:center="centerUpdate"
							@update:zoom="zoomUpdate"
							>
								<l-tile-layer
								:url="url"
								/>
								<l-marker :lat-lng="withPopup">
								</l-marker>
							</l-map>
						</div>
					</md-tab>
				</md-tabs>

				<md-dialog-actions>
					<md-button class="md-primary" @click="showDetailsForm = false">Fermer</md-button>
				</md-dialog-actions>
			</md-dialog>

			<md-button class="md-primary md-raised" @click="showAddForm = true">Ajouter un restaurant</md-button>
			<md-snackbar :md-active.sync="restaurantSauvegarder" md-position="left">Le restaurant est enregistré !</md-snackbar>
			<md-snackbar :md-active.sync="restaurantSupprimer" md-position="left">Le restaurant est supprimé !</md-snackbar>
			<md-snackbar :md-active.sync="restaurantModifier" md-position="left">Le restaurant est modifié !</md-snackbar>

			<br>
			<md-table v-model="restaurants" md-sort="nom" md-sort-order="asc" md-card md-fixed-header>
				<md-table-toolbar>
					<div class="md-toolbar-section-start">
						<h1 class="md-title">Nombre de restaurants : {{nbRestaurantsTotal}}</h1>
						<h1 class="md-title">Nombre de pages total : {{nbPagesTotal}}</h1>
						<h1 class="md-title">Page courante : {{page}}</h1>
					</div>

					<md-field md-clearable class="md-toolbar-section-end">
						<md-input placeholder="Chercher par nom" v-model="nomRestauRecherche" @input="chercherRestaurants()" />
					</md-field>
				</md-table-toolbar>

				<md-table-empty-state
				md-label="Voulez-vous ajouter un restaurant ?">
					<md-button class="md-primary md-raised" @click="showAddForm = true">Ajouter un restaurant</md-button>
				</md-table-empty-state>

				<md-table-row slot="md-table-row" slot-scope="{ item }">
					<md-table-cell md-label="Nom" md-sort-by="name"><span class="md-title">{{item.name}}</span></md-table-cell>
					<md-table-cell md-label="Cuisine" md-sort-by="cuisine"><span class="md-title">{{item.cuisine}}</span></md-table-cell>
					<md-table-cell md-label="Ville" md-sort-by="borough"><span class="md-title">{{item.borough}}</span></md-table-cell>
					<md-table-cell md-label="Édition">
						<md-button class="md-raised md-primary" @click="afficheEditRestaurant(item)">Modification</md-button>
						<md-button class="md-raised md-accent" @click="supprimerRestaurant(item)">Supression</md-button>
					</md-table-cell>
					<md-table-cell md-label="Plus d'infos">
						<md-button class="md-fab md-primary" @click="detailsRestaurant(item)">
							<md-icon>add</md-icon>
						</md-button>
					</md-table-cell>
					
				</md-table-row>
			</md-table>

			<br>
			<md-button :disabled="page===0" @click="pagePrecedente()" class="md-raised">Précédent</md-button>
			<md-button :disabled="page===nbPagesTotal" @click="pageSuivante()" class="md-raised">Suivant</md-button>
			<p class="md-title">Nombre de restaurants par page : {{pagesize}}
			<input 
				@input="getRestaurantsFromServer()"
				type="range" min=5 max=1000 v-model="pagesize"
			></p>
		</md-app-content>
	</md-app>
  </div>
</template>

<script>
import _ from "lodash";
import { LMap, LMarker, LTileLayer } from 'vue2-leaflet';
import { latLng } from "leaflet";
import { Icon } from 'leaflet';
import moment from 'moment';

delete Icon.Default.prototype._getIconUrl;
Icon.Default.mergeOptions({
  iconRetinaUrl: require('leaflet/dist/images/marker-icon-2x.png'),
  iconUrl: require('leaflet/dist/images/marker-icon.png'),
  shadowUrl: require('leaflet/dist/images/marker-shadow.png'),
});

export default {
	name: 'ListeDesRestaurants',
	components: {
		LMap,
		LMarker,
		LTileLayer
	},
	data: function() {
		return {
			restaurants: [],
			nom: "",
			cuisine: "",
			editnom: "",
			editcuisine: "",
			ville: "",
			adresse: [],
			grades: [],
			nbRestaurantsTotal: 0,
			page: 0,
			pagesize: 10,
			nbPagesTotal: 0,
			msg: "",
			nomRestauRecherche: "",
			showAddForm: false,
			showEditForm: false,
			showDetailsForm: false,
			restaurantSauvegarder: false,
			restaurantSupprimer: false,
			restaurantModifier: false,
			editedID: "",
			zoom: 13,
			center: null,
			url: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
			withPopup: null,
			currentZoom: 11,
			currentCenter: null,
			mapOptions: {
				zoomSnap: 0.5
			},
		}
	},
	mounted() {
		console.log("AVANT AFFICHAGE");
		this.getRestaurantsFromServer();
	},
	methods: {
		format_date(value){
			if (value) {
				return moment(String(value)).format('DD/MM/YYYY')
			}
		},
		zoomUpdate(zoom) {
			this.currentZoom = zoom;
		},
		centerUpdate(center) {
			this.currentCenter = center;
		},
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
		afficheEditRestaurant(event) {
			this.showEditForm = true;
			this.editnom = event.name;
			this.editcuisine = event.cuisine;
			this.editedID = event._id;
		},
		detailsRestaurant(item) {
			this.showDetailsForm = true;
			this.nom = item.name;
			this.cuisine = item.cuisine;
			this.ville = item.borough;
			this.adresse = item.address
			this.grades = item.grades;
			console.log(item);

			this.center = latLng(item.address.coord[1], item.address.coord[0]);
			this.withPopup = latLng(item.address.coord[1], item.address.coord[0]);
			this.currentCenter = latLng(item.address.coord[1], item.address.coord[0]);
		},
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

			window.setTimeout(() => {
				this.restaurantSupprimer = true
			}, 1500)

			this.restaurantSupprimer = false;
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
		modifierRestaurant(event) {
			// Récupération du formulaire. Pas besoin de document.querySelector
			// ou document.getElementById puisque c'est le formulaire qui a généré
			// l'événement
			let form = event.target;

			// Récupération des valeurs des champs du formulaire
			// en prévision d'un envoi multipart en ajax/fetch
			let donneesFormulaire = new FormData(form);

			let id = this.editedID;

			let url = "http://localhost:8080/api/restaurants/" + id;

			fetch(url, {
				method: "PUT",
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
				this.restaurantModifier = true
			}, 1500)

			this.editnom = "";
			this.editcuisine = "";
			this.restaurantModifier = false;
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
