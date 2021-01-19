<template>
	<div :class="isLoaded ? 'reveal is-loaded' : 'reveal'">
		<div class="has-text-centered mb-lg">
			<p class="is-size-4">{{ resource.name }}</p>
		</div>
		
		<tabs class="is-marginless" :tabs="[
			{route: 'forms.edit', label: 'former::forms.single', active: true},
			{route: 'forms.fields', label: 'former::fields.multiple', active: false}
		]"></tabs>

		<div class="paper">
			<form method="POST" action="/api/forms" @submit.prevent="requestUpdate('forms')" autocomplete="off">
				
				<div class="paper__body paper__body--noside">
					<div class="paper__main">
						<FormBody :schema="schema" v-model="form" :readonly="!$can('write_forms')"/>
					</div>
				</div>

				<SubmitFooter v-if="$can('write_forms')" :config="{icon: 'save'}" v-model="form"></SubmitFooter>

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
		titleLabel: 'former::forms.edit',
		breadcrumbs: [
			{to: { name: 'forms.index'}, text: this.$root.trans.get('former::forms.multiple')}
		],
		guardedBy: 'read_forms',
		showRoute: 'forms',
		form: new Form({name: '', description: ''}),
		schema: [
			{
				type: 'TextField',
				name: 'name',
				label: this.$root.trans.get('validation.attributes.name'),
				options: {required: true}
			},
			{
				type: 'TextareaField',
				name: 'description',
				label: this.$root.trans.get('validation.attributes.description'),
				options: {}
			}
		]
	}}
}
</script>