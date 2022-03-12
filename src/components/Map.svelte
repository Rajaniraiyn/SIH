<script>
  import { onMount, onDestroy } from "svelte";
  import {
    Map,
    NavigationControl,
    Marker,
    Popup,
    FullscreenControl,
  } from "maplibre-gl";
  import "maplibre-gl/dist/maplibre-gl.css";

  let map;
  let mapContainer;

  const evC = [
    {
      lng: 11.00902,
      lat: 76.95997,
      name: "Brookfields Mall",
      color: "#FF0000",
    },
    {
      lng: 11.022849,
      lat: 76.951635,
      name: "SR Tranzcars, Saibaba Koil",
      color: "#FF0000",
    },
    {
      lng: 11.00269216,
      lat: 77.0387079,
      name: "IOCL Shanthi Social Services, Singanallur",
      color: "#FF0000",
    },
    {
      lng: 11.0706702,
      lat: 76.9988664,
      name: "DC",
      color: "#FFA500",
    },
    {
      lng: 11.031058,
      lat: 77.038549,
      name: "DC",
      color: "#FFA500",
    },
    {
      lng: 11.025281381418203,
      lat: 77.01098024779436,
      name: "DC",
      color: "#FFA500",
    },
    {
      lng: 10.999479851953545,
      lat: 77.02488288641993,
      name: "DC",
      color: "#FFA500",
    },
    {
      lng: 11.0162618,
      lat: 76.9699463,
      name: "Canditate Point",
      color: "#FFFF00",
    },
    {
      lng: 11.017114,
      lat: 76.953749,
      name: "Canditate Point",
      color: "#FFFF00",
    },
    {
      lng: 11.055021,
      lat: 76.994646,
      name: "Canditate Point",
      color: "#FFFF00",
    },
    {
      lng: 11.013017,
      lat: 76.985896,
      name: "Canditate Point",
      color: "#FFFF00",
    },
  ];

  onMount(() => {
    const { env } = _process;
    const apiKey = env.API_KEY;

    if (!apiKey) {
      throw new Error("You need to configure env API_KEY first, see README");
    }

    const initialState = { lng: 0, lat: 0, zoom: 12 };

    map = new Map({
      container: mapContainer,
      style: `https://api.maptiler.com/maps/streets/style.json?key=${apiKey}`,
      center: [initialState.lng, initialState.lat],
      zoom: initialState.zoom,
    });
    map.addControl(new NavigationControl(), "top-right");
    map.addControl(
      new FullscreenControl({ container: document.querySelector("#map") })
    );

    // Add markers based on evC
    evC.forEach((ev) => {
      new Marker({
        color: ev.color,
      })
        .setLngLat([ev.lat, ev.lng])
        .setPopup(new Popup().setText(ev.name))
        .addTo(map);

      map.setCenter([ev.lat, ev.lng]);
    });

    // gets the current location, create a marker with red color and seek to it
    navigator.geolocation.getCurrentPosition(
      (position) => {
        const { latitude, longitude } = position.coords;
        new Marker({
          color: "#663399",
        })
          .setLngLat([longitude, latitude])
          .setPopup(new Popup().setText("Current Location"))
          .addTo(map);

        // map.setCenter([longitude, latitude]);
      },
      (error) => {
        console.error(error);
      }
    );
  });

  onDestroy(() => {
    map.remove();
  });
</script>

<div class="map-wrap">
  <div class="map" id="map" bind:this={mapContainer} />
</div>

<style>
  .map-wrap {
    position: relative;
    width: 100%;
    height: 100vh;
  }

  .map {
    position: absolute;
    width: 100%;
    height: 100%;
  }
</style>
