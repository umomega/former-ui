<template>
	<div :class="isLoaded ? 'paper reveal is-loaded' : 'paper reveal'">
		<form method="POST" action="/api/forms" @submit.prevent="requestStoreDynamic()" autocomplete="off">
			
			<div class="paper__body paper__body--noside">
				<div class="paper__main">
					<FormBody :schema="schema" v-model="form" :readonly="false"/>
				</div>
			</div>

			<SubmitFooter :config="{icon: 'plus'}" v-model="form"></SubmitFooter>

		</form>
	</div>
</template>

<script>
import {View, Storer, Shower, FormBody, Form, SubmitFooter, RequiresPermissions} from 'umomega-foundation'

export default {
	mixins: [ View, RequiresPermissions, Shower, Storer ],
	components: { FormBody, SubmitFooter },
	data() { return {
		titleLabel: 'former::fields.create',
		breadcrumbs: [
			{to: { name: 'forms.index'}, text: this.$root.trans.get('former::forms.multiple')}
		],
		guardedBy: 'write_forms',
		showRoute: 'forms',
		form: new Form({name: '', label: '', type: '', description: '', is_visible: true, rules: '', default_value: '', options: ''}),
		preventPopulateForm: true,
		schema: [
			{
				type: 'TextField',
				name: 'name',
				label: this.$root.trans.get('validation.attributes.name'),
				options: {required: true},
				hint: this.$root.trans.get('former::fields.hint_name')
			},
			{
				type: 'TextField',
				name: 'label',
				label: this.$root.trans.get('validation.attributes.label'),
				options: {required: true},
				hint: this.$root.trans.get('former::fields.hint_label')
			},
			{
				type: 'SelectField',
				name: 'type',
				label: this.$root.trans.get('validation.attributes.type'),
				options: {
					required: true,
					choices: [
						{ value: 'TextField', label: this.$root.trans.get('former::fields.TextField') },
						{ value: 'TextareaField', label: this.$root.trans.get('former::fields.TextareaField') },
						{ value: 'TextEditorField', label: this.$root.trans.get('former::fields.TextEditorField') },
						{ value: 'MediaField', label: this.$root.trans.get('former::fields.MediaField') },
						{ value: 'ContentRelationField', label: this.$root.trans.get('former::fields.ContentRelationField') },
						{ value: 'CodeField', label: this.$root.trans.get('former::fields.CodeField') },
						{ value: 'DatetimeField', label: this.$root.trans.get('former::fields.DatetimeField') },
						{ value: 'CheckboxField', label: this.$root.trans.get('former::fields.CheckboxField') },
						{ value: 'SelectField', label: this.$root.trans.get('former::fields.SelectField') },
						{ value: 'NumberField', label: this.$root.trans.get('former::fields.NumberField') },
						{ value: 'RangeField', label: this.$root.trans.get('former::fields.RangeField') },
						{ value: 'ColorField', label: this.$root.trans.get('former::fields.ColorField') },
						{ value: 'PasswordField', label: this.$root.trans.get('former::fields.PasswordField') },
						{ value: 'RelationField', label: this.$root.trans.get('former::fields.RelationField') },
						{ value: 'EmailField', label: this.$root.trans.get('former::fields.EmailField') }
					]
				}
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
			if(n.id) this.breadcrumbs.push({to: { name: 'forms.fields', params: { id: n.id }}, text: n.name})
		}
	},
	methods: {
		requestStoreDynamic() {
			this.requestStore('forms/' + this.resource.id + '/fields', 'formfields.edit', this.resource.id)
		}
	}
};
</script>