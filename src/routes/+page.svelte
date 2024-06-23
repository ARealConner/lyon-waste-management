<script>
	import { onMount } from 'svelte';
	import 'leaflet/dist/leaflet.css';

	let L;
	let map;

	onMount(async () => {
		if (typeof window !== 'undefined') {
			// Dynamically import Leaflet on the client-side
			L = await import('leaflet');

			// Initialize the map
			map = L.map('map', {
				preferCanvas: true // Enable canvas rendering
			}).setView([45.75, 4.85], 13);

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

			// Create a canvas renderer for the markers
			const canvasRenderer = L.canvas();

			// Create marker layers for compost and garbage bins
			const compostMarkers = L.geoJSON(compostBins, {
				pointToLayer: (feature, latlng) =>
					L.circleMarker(latlng, {
						renderer: canvasRenderer
					})
			}).addTo(map);

			const garbageMarkers = L.geoJSON(garbageBins, {
				pointToLayer: (feature, latlng) =>
					L.circleMarker(latlng, {
						renderer: canvasRenderer
					})
			}).addTo(map);
		}
	});
</script>

<div id="map"></div>

<style>
	#map {
		height: 100vh;
	}
</style>
