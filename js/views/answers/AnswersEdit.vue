<template>
	<div :class="isLoaded ? 'reveal is-loaded' : 'reveal'">
		<div class="has-text-centered mb-lg">
			<p class="is-size-4">{{ resource.uuid }}</p>
			<p class="is-size-8 has-text-weight-bold has-color-grey-darker is-uppercase" v-text="description"></p>
		</div>

		<div class="paper">
			<form method="POST" action="/api/answers" @submit.prevent="requestUpdate('answers')" autocomplete="off">
				
				<div class="paper__body paper__body--noside">
					<div class="paper__main">
						<FormBody :schema="schema" v-model="form" :readonly="!$can('write_answers')"/>
					</div>
				</div>

				<SubmitFooter v-if="$can('write_answers')" :config="{icon: 'save'}" v-model="form"></SubmitFooter>

			</form>
		</div>
	</div>
</template>

<script>
import {View, Updater, Shower, Tabs, FormBody, Form, SubmitFooter, RequiresPermissions, format_date} from 'umomega-foundation'

export default {
	mixins: [ View, Updater, Shower, RequiresPermissions ],
	components: { FormBody, SubmitFooter, Tabs },
	data() { return {
		titleLabel: 'former::answers.edit',
		breadcrumbs: [
			{to: { name: 'answers.index'}, text: this.$root.trans.get('former::answers.multiple')}
		],
		guardedBy: 'read_answers',
		showRoute: 'answers',
		form: new Form({ip: '', status: 30, notes: ''}),
		preventPopulateForm: true,
		schema: [
			{
				type: 'SelectField',
				name: 'status',
				label: this.$root.trans.get('validation.attributes.status'),
				options: {
					required: true,
					choices: [
						{ value: 20, label: this.$root.trans.get('former::answers.status_20')},
						{ value: 30, label: this.$root.trans.get('former::answers.status_30')},
						{ value: 40, label: this.$root.trans.get('former::answers.status_40')},
						{ value: 50, label: this.$root.trans.get('former::answers.status_50')}
					]
				}
			},
			{
				type: 'TextareaField',
				name: 'notes',
				label: this.$root.trans.get('validation.attributes.notes'),
				options: {}
			},
			{
				type: 'TextField',
				name: 'ip',
				label: this.$root.trans.get('validation.attributes.ip_address'),
				options: {readonly: true}
			}
		]
	}},
	methods: {
		makeReadableDate(date) {
			return date == undefined ? '' : format_date(date)
		},
		registerResourceLoaded(self) {
			Event.$off('resource-loaded')
			Event.$on('resource-loaded', function(data) {
				data.schema.schema.forEach(function(field, i) {
					if(field.options == null) {
						field.options = {readonly: true}
					} else {
						field.options.readonly = true
					}

					self.schema.push(field)
				})

				self.form.populate(data)
			})
		},
	},
	computed: {
		description() {
			if(this.resource.form == undefined) return

			return this.resource.form.name + ' . ' + this.makeReadableDate(this.resource.created_at)
		}
	},
	mounted() {
		const self = this

		self.registerResourceLoaded(self)
	},
	beforeDestroy() {
		Event.$off('resource-loaded')
	}
}
</script>