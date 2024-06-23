<script>
	import { onMount } from 'svelte';

	// Dynamically import Leaflet only on the client side
	let L;
	let map;

	onMount(async () => {
		if (typeof window !== 'undefined') {
			L = await import('leaflet');
			await import('leaflet/dist/leaflet.css');

			// Initialize the map
			map = L.map('map').setView([45.75, 4.85], 13);

			// Add a tile layer
			L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
				attribution: '&copy; OpenStreetMap contributors'
			}).addTo(map);

			// Fetch GeoJSON data
			const compostBins = await (
				await fetch(
					'https://data.grandlyon.com/geoserver/metropole-de-lyon/ows?SERVICE=WFS&VERSION=2.0.0&request=GetFeature&typename=metropole-de-lyon:gic_collecte.bornecompost&outputFormat=application/json&SRSNAME=EPSG:4171&startIndex=0&sortBy=gid'
				)
			).json();

			const garbageBins = await (
				await fetch(
					'https://data.grandlyon.com/geoserver/metropole-de-lyon/ows?SERVICE=WFS&VERSION=2.0.0&request=GetFeature&typename=metropole-de-lyon:gic_collecte.bornecompost&outputFormat=application/json&SRSNAME=EPSG:4171&startIndex=0&sortBy=gid'
				)
			).json();

			// Add GeoJSON layer to the map
			L.geoJSON(compostBins).addTo(map);
			L.geoJSON(garbageBins).addTo(map);
		}
	});
</script>

<div id="map"></div>

<style>
	#map {
		height: 100vh;
	}
</style>
