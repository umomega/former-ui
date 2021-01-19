<template>
	<div :class="isLoaded ? 'reveal is-loaded' : 'reveal'">
		<div class="has-text-centered mb-lg">
			<p class="is-size-4">{{ resource.name }}</p>
		</div>

		<tabs class="is-marginless" :tabs="[
			{route: 'forms.edit', label: 'former::forms.single', active: false},
			{route: 'forms.fields', label: 'former::fields.multiple', active: true}
		]"></tabs>

		<div class="paper">
			<div class="paper__body paper__body--noside">
				<div class="paper__main">
					<div class="relation-field">
						<p class="pt-xxl pb-xxl pl-md pr-md has-text-centered" v-if="fields.length == 0" v-text="trans.get('former::forms.no_fields_found')"></p>
						<draggable v-if="$can('write_forms')" :tag="'ul'" v-model="fields" :animate="200" handle=".handle" @end="saveSortedFields">
							<li class="related-item related-item--handled" v-for="(field, i) in fields" :key="field.id">
								<span class="handle icon is-medium has-color-blue">
									<i class="fas fa-grip-lines"></i>
								</span>
								<router-link :to="{ name: 'formfields.edit', params: {parent: field.form_id, id: field.id} }">{{ field.label }}</router-link> <span class="has-color-grey-darker">[{{ trans.get('former::fields.' + field.type) }}]</span>
								<div class="dropdown is-hoverable is-right" v-if="$can('write_forms')">
									<div class="dropdown-trigger">
										<button class="button is-compact is-borderless" aria-haspopup="true">
											<i class="icon fas fa-ellipsis-v"></i>
										</button>
									</div>
									<div class="dropdown-menu" role="menu">
										<div class="dropdown-content">
											<div class="dropdown-label" v-text="trans.get('foundation::general.options')"></div>
											<router-link :to="{ name: 'formfields.edit', params: {parent: field.form_id, id: field.id} }" class="dropdown-item">
												<i class="icon fas fa-edit has-color-grey-darker"></i> {{ trans.get('former::fields.edit') }}
											</router-link>
											<a href="#" class="dropdown-item has-color-danger" @click.prevent="openDeleteModal({ bulk: false, route: 'forms/' + field.form_id + '/fields/' + field.id })">
												<i class="icon fas fa-trash"></i> {{ trans.get('former::fields.delete') }}
											</a>
										</div>
									</div>
								</div>
							</li>
						</draggable>
						<ul v-else>
							<li class="related-item related-item--handled" v-if="fields.length > 0" v-for="(field, i) in fields" :key="field.id">
								<router-link :to="{ name: 'formfields.edit', params: {parent: field.form_id, id: field.id} }">{{ field.label }}</router-link> <span class="has-color-grey-darker">[{{ trans.get('former::fields.' + field.type) }}]</span>
							</li>
						</ul>
					</div>
				</div>
			</div>
			<div class="paper__footer" v-if="$can('write_forms') && resource.id">
				<div class="field has-addons is-pulled-right">
					<div class="control">
						<router-link class="button icon-only-wide is-primary" :to="{ name: 'formfields.create', params: {id: resource.id}}">
							<i class="icon fas fa-plus"></i>
						</router-link>
					</div>
				</div>
			</div>
		</div>
	</div>
</template>

<script>
import {View, Shower, Tabs, DatableDropdown, format_date, RequiresPermissions, assess_error} from 'umomega-foundation'
import draggable from 'vuedraggable'

export default {
	mixins: [ View, Shower, RequiresPermissions ],
	components: { DatableDropdown, Tabs, draggable },
	data() { return {
		titleLabel: 'former::fields.multiple',
		breadcrumbs: [
			{to: { name: 'forms.index'}, text: this.$root.trans.get('former::forms.multiple')}
		],
		showRoute: 'forms',
		guardedBy: 'read_forms',
		fields: []
	}},
	created() {
		this.load()

		var self = this

		Event.$off('resources-deleted')
		Event.$on('resources-deleted', function(data) {
			self.load()
		})
	},
	methods: {
		load() {
			var self = this;

			axios.get(api_url_with_token('forms/' + self.$route.params.id  + '/fields'))
				.then(function(response) {
					self.fields = response.data;
					self.isLoaded = true
				})
				.catch(function(error) { assess_error(error) })
		},
		saveSortedFields() {
			const self = this
			
			axios.put(api_url_with_token('forms/' + self.$route.params.id + '/fields/sort'), {sorted: self.fields.map(function(f) { if(f) return f.id })})
				.catch(function(error) { assess_error(error) })
		},
		openDeleteModal(payload) {
			Event.$emit('delete-modal-open', payload)
		}
	}
}
</script>