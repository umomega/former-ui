<template>
	<Datable
		:defaultkey="'name'" :defaultdir="'asc'"
		:route="'forms.index'"
		:sortables="[
			{key: 'name', label: 'validation.attributes.name'}
		]"
		:headers="['former::fields.multiple']"
		:createroutename="'forms.create'"
		:indexloadroute="'forms'"
		:searchloadroute="'forms/search'"
		:bulkdeleteroute="'forms/bulk'"
		:alllabel="'former::forms.all'"
		:canwrite="$can('write_forms')"
	>
		<template v-slot:default="slotProps">
			<td><router-link :to="{ name: 'forms.edit', params: {id: slotProps.row.id} }" v-text="slotProps.row.name" /></td>
			<td><router-link :to="{ name: 'forms.fields', params: {id: slotProps.row.id} }" v-text="trans.get('former::fields.multiple')" /></td>
			<DatableDropdown :can="$can('write_forms')" :editroute="{ name: 'forms.edit', params: {id: slotProps.row.id} }" :editlabel="trans.get('former::forms.edit')" :deletepayload="{ bulk: false, route: 'forms/' + slotProps.row.id }" :deletelabel="'former::forms.delete'">
				<a href="#" class="dropdown-item" @click.prevent="duplicate('forms/' + slotProps.row.id + '/duplicate')">
					<i class="icon fas fa-copy has-color-grey-darker"></i> {{ trans.get('former::forms.duplicate') }}
				</a>
			</DatableDropdown>
		</template>
	</Datable>
</template>

<script>
import {View, Datable, DatableDropdown, RequiresPermissions, assess_error} from 'umomega-foundation'

export default {
	mixins: [ View, RequiresPermissions ],
	components: { Datable, DatableDropdown },
	data() { return {
		titleLabel: 'former::forms.manage',
		guardedBy: 'read_forms'
	}},
	methods: {
		duplicate(route) {
			const self = this

			axios.post(api_url_with_token(route))
				.then(function(response) {
					self.notifier.success(response.data.message)
					router.push({ name: 'forms.edit', params: {id: response.data.payload.id} })
				})
				.catch(function(error) { assess_error(error) })
		}
	}
}
</script>