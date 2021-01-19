<template>
	<span>
		{{ trans.get('former::fields.event_updated') }}<span v-if="activity.subject">: <router-link :to="{ name: 'formfields.edit', params: {parent: activity.subject.form_id, id: activity.subject_id} }" v-text="activity.subject.label"></router-link></span>
	</span>
</template>

<script>
export default {props: ['activity']}
</script>