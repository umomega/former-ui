<template>
	<div :class="isLoaded ? 'reveal is-loaded' : 'reveal'">
		<div class="has-text-centered mb-lg">
			<p class="is-size-4">{{ resource.name }}</p>
		</div>

		<div class="paper">
			<form method="POST" action="/api/forms" @submit.prevent="requestUpdateDynamic()" autocomplete="off">
				
				<div class="paper__body paper__body--noside">
					<div class="paper__main">
						<FormBody :schema="schema" v-model="form" :readonly="!$can('write_forms')"/>
					</div>
				</div>

				<SubmitFooter v-if="$can('write_forms')" :config="{icon: 'save'}" v-model="form">
					<div class="control" v-if="resource.form_id">
						<router-link class="button icon-only is-warning" :to="{ name: 'formfields.create', params: {id: resource.form_id} }">
							<i class="icon fas fa-plus"></i>
						</router-link>
					</div>
				</SubmitFooter>

			</form>
		</div>
	</div>
</template>

<script>
import {View, Updater, Shower, Tabs, FormBody, Form, SubmitFooter, RequiresPermissions} from 'umomega-foundation'

export default {
	mixins: [ View, Updater, Shower, RequiresPermissions ],
	components: { FormBody, SubmitFooter, Tabs },
	data() { return {
		titleLabel: 'former::fields.edit',
		breadcrumbs: [
			{to: { name: 'forms.index'}, text: this.$root.trans.get('former::forms.multiple')}
		],
		guardedBy: 'read_forms',
		showRoute: 'forms/' + this.$route.params.parent + '/fields',
		form: new Form({label: '', description: '', is_visible: true, rules: '', default_value: '', options: ''}),
		schema: [
			{
				type: 'TextField',
				name: 'label',
				label: this.$root.trans.get('validation.attributes.label'),
				options: {required: true},
				hint: this.$root.trans.get('former::fields.hint_label')
			},
			{
				type: 'TextareaField',
				name: 'description',
				label: this.$root.trans.get('validation.attributes.description'),
				options: {}
			},
			{
				type: 'CheckboxField',
				name: 'is_visible',
				label: this.$root.trans.get('validation.attributes.is_visible'),
				options: { required: true }
			},
			{
				type: 'TextField',
				name: 'rules',
				label: this.$root.trans.get('validation.attributes.rules'),
				options: {},
				hint: this.$root.trans.get('former::fields.hint_rules')
			},
			{
				type: 'TextField',
				name: 'default_value',
				label: this.$root.trans.get('validation.attributes.default_value'),
				options: {},
				hint: this.$root.trans.get('former::fields.hint_default_value')
			},
			{
				type: 'TextareaField',
				name: 'options',
				label: this.$root.trans.get('validation.attributes.options'),
				options: {},
				hint: this.$root.trans.get('former::fields.hint_options')
			}
		]
	}},
	watch: {
		resource(n) {
			this.breadcrumbs.splice(1)
			this.breadcrumbs.push({to: { name: 'forms.fields', params: { id: n.form.id }}, text: n.form.name})
		}
	},
	methods: {
		requestUpdateDynamic() {
			this.requestUpdate('forms/' + this.resource.form.id + '/fields')
		}
	}
}
</script>