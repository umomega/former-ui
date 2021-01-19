<template>
	<div :class="isLoaded ? 'paper reveal is-loaded' : 'paper reveal'">
		<form method="POST" action="/api/forms" @submit.prevent="requestStore('forms', 'forms.edit')" autocomplete="off">
			
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
import {View, Storer, FormBody, Form, SubmitFooter, RequiresPermissions} from 'umomega-foundation'

export default {
	mixins: [ View, RequiresPermissions, Storer ],
	components: { FormBody, SubmitFooter },
	data() { return {
		titleLabel: 'former::forms.create',
		breadcrumbs: [
			{to: { name: 'forms.index'}, text: this.$root.trans.get('former::forms.multiple')}
		],
		guardedBy: 'write_forms',
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
};
</script>