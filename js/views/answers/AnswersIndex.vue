<template>
	<Datable
		:defaultkey="'created_at'" :defaultdir="'desc'"
		:route="'answers.index'"
		:sortables="[
			{key: 'uuid', label: 'validation.attributes.name'},
			{key: 'created_at', label: 'validation.attributes.created_at'}
		]"
		:headers="[
			'validation.attributes.status',
			'former::forms.single'
		]"
		:createroutename="false"
		:indexloadroute="'answers'"
		:searchloadroute="'answers/search'"
		:bulkdeleteroute="'answers/bulk'"
		:alllabel="'former::answers.all'"
		:canwrite="$can('write_answers')"
		:filterable="true"
		:filters="[
			{value: 20, label: trans.get('former::answers.status_20')},
			{value: 30, label: trans.get('former::answers.status_30')},
			{value: 40, label: trans.get('former::answers.status_40')},
			{value: 50, label: trans.get('former::answers.status_50')}
		]"
	>
		<template v-slot:default="slotProps">
			<td><router-link :to="{ name: 'answers.edit', params: {id: slotProps.row.id} }" v-text="slotProps.row.uuid" /></td>
			<td><span class="is-size-7" v-text="makeReadableDate(slotProps.row.created_at)"></span></td>
			<td><span :class="determineClass(slotProps.row.status)">{{ trans.get('former::answers.status_' + slotProps.row.status) }}</span></td>
			<td><span class="is-size-8 has-color-gray">{{ slotProps.row.form.name }}</span></td>
			<DatableDropdown :can="$can('write_answers')" :editroute="{ name: 'answers.edit', params: {id: slotProps.row.id} }" :editlabel="trans.get('former::answers.edit')" :deletepayload="{ bulk: false, route: 'answers/' + slotProps.row.id }" :deletelabel="'former::answers.delete'">
			</DatableDropdown>
		</template>
	</Datable>
</template>

<script>
import {View, Datable, DatableDropdown, RequiresPermissions, assess_error, format_date} from 'umomega-foundation'

export default {
	mixins: [ View, RequiresPermissions ],
	components: { Datable, DatableDropdown },
	data() { return {
		titleLabel: 'former::answers.manage',
		guardedBy: 'read_answers'
	}},
	methods: {
		makeReadableDate(date) {
			return format_date(date)
		},
		determineClass(status) {
			const statuses = {
				20: 'has-color-grey-lighter',
				30: 'has-color-warning',
				40: 'has-color-success',
				50: 'has-color-grey-light'
			}
			return 'is-size-8 has-text-weight-bold ' + statuses[status]
		}
	}
}
</script>