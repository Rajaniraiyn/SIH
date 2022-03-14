<script>
  import { onMount, onDestroy } from "svelte";
  import {
    Map,
    NavigationControl,
    Marker,
    Popup,
    FullscreenControl,
    GeolocateControl,
  } from "maplibre-gl";
  import "maplibre-gl/dist/maplibre-gl.css";

  let map;
  let mapContainer;
  let geoJSONData;

  const evCN = [
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

  const evC = [
    {
      lng: 11.10618,
      lat: 77.35042,
      name: "Sri Sakthi Cinemas",
      provider: "Zeon Chargers",
      ports: 1,
      chargingType: "Fast"
    },
    {
      lng: 11.175093,
      lat: 77.263374,
      name: "SREE ANNAPOORNA HOTEL",
      provider: "Zeon Chargers",
      ports: 1,
      chargingType: "Fast"
    },
    {
      lng: 11.213022,
      lat: 77.441968,
      name: "IOCL COCO",
      provider: "Tata Power - CCS2 & slow chargers",
      ports: 1,
      chargingType: "Normal"
    },
    {
      lng: 11.30199446,
      lat: 77.60004376,
      name: "RMS Towers (Ero_G Electric Charging Station)",
      provider: "Relux Electric",
      ports: 1,
      chargingType: "Fast"
    },
    {
      lng: 11.519342,
      lat: 77.935042,
      name: "SHREE SARAVANA BHAVAN",
      provider: "Zeon Chargers",
      ports: 4,
      chargingType: "Fast"
    },
    {
      lng: 11.5213,
      lat: 77.93434,
      name: "Sree Saravana Bhavan",
      provider: "Zeon Chargers",
      ports: 1,
      chargingType: "Fast"
    },
    {
      lng: 11.4922712,
      lat: 78.1535774,
      name: "Adyar Ananda Bhavan",
      provider: "Zeon Chargers",
      ports: 3,
      chargingType: "Fast"
    },
    {
      lng: 11.682985,
      lat: 78.1260402,
      name: "True Sai Motors",
      provider: "Tata Power - CCS2 & slow chargers",
      ports: 2,
      chargingType: "Normal"
    },
    {
      lng: 10.9984009,
      lat: 76.9765316,
      name: "Richy Rich",
      provider: "Ather",
      ports: 4,
      portTypes: { AC: 2, Ather: 2},
      chargingType: "Fast"
    },
    {
      lng: 11.00269216,
      lat: 77.0387079,
      name: "IOCL Shanthi Social Services",
      provider: "Tata Power - CCS2 & slow chargers",
    },
    {
      lng: 11.04991,
      lat: 77.05765,
      name: "PPS Motors",
      provider: "Tata Power - CCS2 & slow chargers",
      ports: 2,
      chargingType: "Fast"
    },
  ];

  const color = {
    "Zeon Chargers": "#FF0000",
    "Tata Power - CCS2 & slow chargers": "#FFA500",
    "Relux Electric": "#FFFF00",
    Ather: "#00FF00",
  };

  let journey = [
    {
      lng: 10.9984009,
      lat: 76.9765316,
      name: "Richy Rich",
      provider: "Ather",
    },
    {
      lng: 11.682985,
      lat: 78.1260402,
      name: "True Sai Motors",
      provider: "Tata Power - CCS2 & slow chargers",
    },
  ];

  onMount(() => {
    const { env } = _process;
    const maplibreApiKey = env.MAPLIBRE_API_KEY;
    const mapboxApiKey = env.MAPBOX_API_KEY;

    const initialState = { lng: 0, lat: 0, zoom: 8 };

    map = new Map({
      container: mapContainer,
      style: `https://api.maptiler.com/maps/streets/style.json?key=${maplibreApiKey}`,
      center: [initialState.lng, initialState.lat],
      zoom: initialState.zoom,
    });
    map.addControl(new FullscreenControl({ container: mapContainer }));
    map.addControl(new NavigationControl(), "top-right");
    map.addControl(new GeolocateControl(), "bottom-right");

    // Add markers based on evC
    evC.forEach((ev) => {
      const { lng, lat, name, provider } = ev;
      new Marker({
        color: color[provider],
      })
        .setLngLat([lat, lng])
        .setPopup(new Popup().setHTML(`<h3>${name}</h3><p>${provider}</p>`))
        .addTo(map);

      map.setCenter([lat, lng]);
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

    // returns the Direction fetch API URL from the coordinates
    const getDirectionURL = (from, to) => {
      return encodeURI(
        `https://api.mapbox.com/directions/v5/mapbox/driving/${from.lat},${from.lng};${to.lat},${to.lng}?geometries=geojson&access_token=${mapboxApiKey}`
      );
    };

    fetch(getDirectionURL(journey[0], journey[1]))
      .then((response) => response.json())
      .then((data) => {
        geoJSONData = data.routes[0].geometry;
        map.addSource("my-route", {
          type: "geojson",
          data: geoJSONData, // <= add data here!
        });

        map.addLayer({
          id: "my-route-layer",
          type: "line",
          source: "my-route", // <= the same source id
          layout: {
            "line-cap": "round",
            "line-join": "round",
          },
          paint: {
            "line-color": "#6084eb",
            "line-width": 8,
          },
        });
      });
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
