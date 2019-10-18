<template>
	<div id="app">
		<h1>Week Slider<span>.</span></h1>

		<!-- WEEK SLIDER -->
		<week-slider></week-slider>
		<week-slider-extended></week-slider-extended>
	</div>
</template>


<script>
import Vue from "vue";
import WeekSlider from "./WeekSlider";
import WeekSliderExtended from "./WeekSliderExtended";

export default {
	components: {
		WeekSlider,
		WeekSliderExtended
	}
};
</script>


<style>
#app {
	color: #2d2d2d;
	font-family: "Open Sans", sans-serif;
}

h1 {
	font-size: 100px;
	font-family: "Pacifico", cursive;
	font-weight: 300;
	text-align: center;
	margin-bottom: 0.2em;
}
h1 span {
	color: #f44336;
}
</style>