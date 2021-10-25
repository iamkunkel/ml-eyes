<template>
	<div>
		<img class="logo" src="@/assets/logo.png">
		<Video />
	</div>
</template>

<script>
import Video from '@/components/Video'
import webNotification from 'simple-web-notification'

export default {
	name: 'App',
	components: {
		Video,
	},
 methods: {
 },
 mounted() {
	//manually ask for notification permissions (invoked automatically if needed and allowRequest=true)
	webNotification.requestPermission(function onRequest(granted) {
		if (granted) {
			console.log('Permission Granted.');
		} else {
			console.log('Permission Not Granted.');
		}
	});
 }
}
</script>

<style>
body {
	background: #0f2027; /* fallback for old browsers */
  background: -webkit-linear-gradient(to right, #0f2027, #203a43, #2c5364); /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(to right, #0f2027, #203a43, #2c5364); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
}

#app {
	display:flex;
	justify-content: center;
	align-items: center;
	width:100%;
	height:100%;
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}

.logo {
	width:50vw;
	max-width:300px;
}
</style>
