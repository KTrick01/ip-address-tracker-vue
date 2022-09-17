<script setup>
import MainComponent from './components/MainComponent.vue';


</script>

<template>
	<MainComponent></MainComponent>
</template>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Rubik:wght@400;500;700&display=swap');
$Dark-Gray: hsl(0, 0%, 59%);
$Very-Dark-Gray: hsl(0, 0%, 17%);

* {
	margin: 0;
	padding: 0;
	box-sizing: border-box;
	list-style: none;
}

body,
html {
	min-height: 100vh;
}
body {
	color: $Very-Dark-Gray;
	font-family: 'Rubik', sans-serif;
	display: grid;
	grid-template-rows: 1fr 2fr;
}
#map {
	background-color: white;
}
</style>
