<template>
	<div v-if="nomdata!==null">
		<form @submit.prevent="saveNoms">
			<div class="section columns is-multiline">
				<nominations-field v-for="(nom,index) in nomdata" :key="index"
					:nom="nom" :category="category" :themes="themes"
					@toggle="updateData(index, $event)" @delete="deleteNom(index)">
				</nominations-field>
			</div>
			<div class="section">
				<button class="button is-primary" @click.prevent="insertField">Add Nomination</button>
				<button type="submit" class="button is-success"
					:class="{'is-loading': submitting}">Save Nominations</button>
			</div>
		</form>
	</div>
	<div v-else>
		Loading ...
	</div>
</template>

<script>
import {mapActions, mapState} from 'vuex';
import NominationsField from './CategoryNominationsField';

export default {
	components: {
		NominationsField,
	},
	props: {
		category: Object,
	},
	data () {
		return {
			nomdata: null,
			submitting: false,
		};
	},
	computed: {
		...mapState([
			'nominations',
			'themes',
		]),
	},
	methods: {
		...mapActions([
			'insertNominations',
			'deleteNominations',
			'getNominations',
			'getThemes',
		]),
		insertField () {
			// fuck lenlo
			this.nomdata.push({
				altName: '',
				categoryID: this.category.id,
				entryType: this.category.entryType,
				anilistID: -1,
				characterID: -1,
				themeID: -1,
				juryRank: -1,
				publicVotes: -1,
				publicSupport: -1,
				staff: '',
				writeup: '',
			});
		},
		saveNoms () {
			this.submitting = true;

			const delPromise = new Promise(async (resolve, reject) => {
				try {
					await this.deleteNominations(this.category.id);
					resolve();
				} catch (err) {
					reject(err);
				}
			});
			delPromise.then(async () => {
				try {
					await this.insertNominations({
						id: this.category.id,
						data: this.nomdata,
					});
				} finally {
					this.submitting = false;
				}
			});
		},
		updateData (index, data) {
			this.nomdata[index] = data;
		},
		deleteNom (index) {
			this.nomdata.splice(index, 1);
		},
	},
	mounted () {
		const nomPromise = new Promise(async (resolve, reject) => {
			try {
				await this.getNominations(this.category.id);
				resolve();
			} catch (err) {
				reject(err);
			}
		});
		const themePromise = new Promise(async (resolve, reject) => {
			try {
				await this.getThemes();
				resolve();
			} catch (err) {
				reject(err);
			}
		});
		Promise.all([nomPromise, themePromise]).then(() => {
			this.nomdata = [];
			for (const nom of this.nominations) {
				this.nomdata.push({
					altName: nom.alt_name,
					categoryID: this.category.id,
					entryType: this.category.entryType,
					anilistID: nom.anilist_id,
					characterID: nom.character_id,
					themeID: nom.theme_id,
					juryRank: nom.rank,
					publicVotes: nom.votes,
					publicSupport: nom.finished,
					staff: nom.staff,
					writeup: nom.writeup,
				});
			}
		});
	},
};
</script>

<style lang="scss">
.submit-wrapper {
	box-shadow: inset 0 1px #dbdbdb;
	text-align: center;
	padding: 5px;
}
</style>
