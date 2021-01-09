<template>
	<div id="app">
		<lesson />
	</div>
</template>

<script>
import lesson from "./components/lesson.vue";

export default {
	name: "App",
	components: {
		lesson,
	},
};
</script>

<style>
#app {
	font-family: Avenir, Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
	margin-top: 60px;
}
</style>
