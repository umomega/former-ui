<template>
	<span>
		{{ trans.get('former::forms.event_duplicated') }}<span v-if="activity.subject">: <router-link :to="{ name: 'forms.edit', params: {id: activity.subject_id} }" v-text="activity.subject.name"></router-link></span>
	</span>
</template>

<script>
export default {props: ['activity']}
</script>