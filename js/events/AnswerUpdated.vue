<template>
	<span>
		{{ trans.get('former::answers.event_updated') }}<span v-if="activity.subject">: <router-link :to="{ name: 'answers.edit', params: {id: activity.subject_id} }" v-text="activity.subject.uuid"></router-link></span>
	</span>
</template>

<script>
export default {props: ['activity']}
</script>