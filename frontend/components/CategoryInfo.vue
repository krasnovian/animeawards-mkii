<template>
	<div class="section">
		<h2 class="title is-3">Category Information</h2>
		<form @submit.prevent="submitEditCategory">
			<div class="field">
				<label class="label">Name</label>
				<div class="control">
					<input class="input" v-model="newCategoryName"/>
				</div>
				<p class="help">The name of the category</p>
			</div>
			<div class="field">
				<label class="label">Entry Type</label>
				<div class="control">
					<div class="select">
						<select v-model="newEntryType">
							<option disabled>Select one</option>
							<option value="shows">Shows</option>
							<option value="characters">Characters</option>
							<option value="vas">VA Performances</option>
							<option value="themes">OPs/EDs</option>
						</select>
					</div>
				</div>
				<p class="help">What type of entries can be included in this category</p>
			</div>
			<div class="field">
				<label class="label">Awards Group</label>
				<div class="control">
					<div class="select">
					<select v-model="newEntryGroup">
						<option disabled>Select one</option>
						<option value="genre">Genre Awards</option>
						<option value="character">Character Awards</option>
						<option value="production">Production Awards</option>
						<option value="test">Test Category</option>
						<option value="main">Main Awards</option>
					</select>
					</div>
				</div>
				<p class="help">What Awards Group this category belongs to (Genre, Character, Production)</p>
			</div>
			<div class="field">
				<div class="control">
					<button
						type="submit"
						class="button is-primary"
						:class="{'is-loading': submitting}"
					>
						Save Changes
					</button>
				</div>
			</div>
		</form>
	</div>
</template>

<script>
import {mapActions} from 'vuex';
export default {
	props: ['category'],
	data () {
		return {
			newCategoryName: this.category.name,
			newEntryType: this.category.entryType,
			newEntryGroup: this.category.awardsGroup,
			submitting: false,
		};
	},
	methods: {
		...mapActions([
			'updateCategory',
		]),
		submitEditCategory () {
			// When this gets implemented, make sure there's a warning when
			// changing the entry type, as that will also erase all current
			// entries.
			this.submitting = true;
			setTimeout(async () => {
				try {
					await this.updateCategory({
						id: this.category.id,
						data: {
							name: this.newCategoryName,
							entryType: this.newEntryType,
							awardsGroup: this.newEntryGroup,
						},
					});
				} finally {
					this.submitting = false;
				}
			});
		},
	},
};
</script>
