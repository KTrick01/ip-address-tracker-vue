<script setup>
import { ref, watch } from 'vue';
import axios from 'axios';
import IconMark from '../assets/icon-location.svg'

const data = ref(null);
const latitud = ref(null);
const longitud = ref(null);
const location = ref(null);
let disabled = ref(true);
const search = ref(null);

const validation = /^((25[0-5]|(2[0-4]|1[0-9]|[1-9]|)[0-9])(\.(?!$)|$)){4}$/;

var greenIcon = L.icon({
	iconUrl: IconMark,
	iconAnchor: [22, 50], // point of the icon which will correspond to marker's location
});

var map = L.map('map', {
	zoomControl: false,
	attributionControl: false,
	keyboard: false,
	scrollWheelZoom: false,
	doubleClickZoom: false
});

const firstUrl = `https://geo.ipify.org/api/v2/country,city?apiKey=at_xDIs6jQSAdLzQleKRD5mZOwFau818&ipAddress=`;

const getData = async (url) => {
	try {
		const res = await axios.get(url);
		data.value = res.data;
		latitud.value = data.value.location.lat;
		longitud.value = data.value.location.lng;
		location.value = `${data.value.location.country}, ${data.value.location.region}`;
		map.setView([`${latitud.value}`, `${longitud.value}`], 16);
		L.tileLayer('https://tile.openstreetmap.org/{z}/{x}/{y}.png', {
			maxZoom: 19,
			attribution: '© OpenStreetMap',
		}).addTo(map);
		L.marker([`${data.value.location.lat}`, `${data.value.location.lng}`], {
			icon: greenIcon,
		}).addTo(map);
	} catch (error) {
		console.log(error);
	}
};

watch(search, () => {
	if (validation.test(search.value)) {
		disabled.value = false;
	} else {
		disabled.value = true;
	}
});

getData(firstUrl);
</script>
<template>
	<span class="loader" v-if="!data"></span>
	<main class="ip" v-else>
		<h1 class="ip__title">IP Address Tracker</h1>
		<div class="ip__search">
			<input
				type="search"
				placeholder="Search for any IP address or domain"
				v-model="search"
			/>
			<button
				@click="getData(firstUrl + search)"
				:disabled="disabled"
				class="ip__button"
			>
				<img src="../assets/icon-arrow.svg" alt="" />
			</button>
		</div>


		<div class="ip__info">
			<fieldset class="ip__address">
				<legend>IP ADDRESS</legend>
				<p>{{ data?.ip }}</p>
			</fieldset>
			<fieldset class="ip__location">
				<legend>Location</legend>
				<p>{{ location }}</p>
			</fieldset>
			<fieldset class="ip__timezone">
				<legend>Timezone</legend>
				<p>{{ data?.location.timezone }}</p>
			</fieldset>
			<fieldset class="ip__isp">
				<legend align="bottom">Isp</legend>
				<p>{{ data?.isp }}</p>
			</fieldset>
		</div>
	</main>
</template>
<style lang="scss">
.loader {
	width: 48px;
	height: 48px;
	display: block;
	margin: 20px auto;
	box-sizing: border-box;
	transform: scale(1.5);
	position: absolute;
	inset: 0;
	top: 50%;

	z-index: 9999;
}
.loader::after {
	content: '';
	box-sizing: border-box;
	width: 48px;
	height: 48px;
	left: 0;
	bottom: 0;
	position: absolute;
	border-radius: 50% 50% 0;
	border: 15px solid rgb(0, 0, 0);
	transform: rotate(45deg) translate(0, 0);
	box-sizing: border-box;
	animation: animMarker 0.4s ease-in-out infinite alternate;
}
.loader::before {
	content: '';
	box-sizing: border-box;
	position: absolute;
	left: 0;
	right: 0;
	margin: auto;
	top: 150%;
	width: 24px;
	height: 4px;
	border-radius: 50%;
	background: rgba(0, 0, 0, 0.2);
	animation: animShadow 0.4s ease-in-out infinite alternate;
}

@keyframes animMarker {
	0% {
		transform: rotate(45deg) translate(5px, 5px);
	}
	100% {
		transform: rotate(45deg) translate(-5px, -5px);
	}
}

@keyframes animShadow {
	0% {
		transform: scale(0.5);
	}
	100% {
		transform: scale(1);
	}
}

.ip {
	grid-area: search;
	padding: 1rem;
	position: relative;
	background-image: url('../assets/pattern-bg.png');
	background-repeat: no-repeat;
	background-size: cover;
	background-position: center;
	display: flex;
	flex-direction: column;
	align-items: center;
	gap: 2rem;
	height: 100%;
	box-shadow: 0 10px 50px rgba(0, 0, 0, 0.192);
	z-index: 999;

	&__info {
		display: flex;
		background-color: white;
		padding: 2rem;
		border-radius: 1rem;
		z-index: 999;
		box-shadow: 0 20px 50px rgba(0, 0, 0, 0.192);
		position: absolute;
		bottom: -25%;
		& legend {
			font-weight: 700;
			border: none;
			text-transform: uppercase;
			font-size: 0.8rem;
			letter-spacing: 2px;
			color: #979797;
		}
		& > fieldset {
			border: none;
			padding: 1rem;
			font-weight: 500;
			font-size: 1.5rem;
		}
		& > fieldset:not(:last-child) {
			border-right: 1px solid #97979741;
		}
		& > * {
			flex-basis: 100%;
		}
	}

	&__search {
		width: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		& > input {
			border-radius: 1rem 0 0 1rem;
			border: none;
			padding: 1.5rem 1rem;
			width: 100%;
			max-width: 600px;
			font-family: 'Rubik', sans-serif;
			font-size: 1rem;
			letter-spacing: 1px;
			outline: none;
		}
	}
	&__button {
		padding: 1.5rem 2rem;
		background-color: black;
		border: none;
		cursor: pointer;
		border-radius: 0 1rem 1rem 0;
		transition: background-color 0.3s;
		&:hover {
			background-color: #3f3f3f;
		}
		&:active img {
			transform: scale(1.5);
		}
		&:disabled {
			opacity: 0.5;
			cursor: not-allowed;
		}
	}
	&__title {
		position: relative;
		color: white;
		font-weight: 500;
		font-size: 2rem;
		text-align: center;
		
	}
}

@media (max-width: 900px) {
	.ip__info {
		flex-direction: column;
		transform: translateY(calc(40% + 1rem));

		& > fieldset:not(:last-child) {
			border-right: none;
		}
		& > fieldset {
			text-align: center;
		}
		& legend {
			margin-left: 50%;
			transform: translateX(-50%);
		}
	}
	.ip__search {
		margin-bottom: 10rem;
	}
}
</style>
