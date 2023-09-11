<script>
// @ts-nocheck

    import axios from 'axios'
    import { createEventDispatcher } from 'svelte'
    import { onMount } from 'svelte'
    import { onDestroy } from 'svelte'
    //import timezones from './timezones.json'

    const dispatch = createEventDispatcher()

    let interval

    const weatherCodes = {
        0: "Clear sky",
        1: "Mainly clear",
        2: "Partly cloudy",
        3: "Overcast",
        45: "Fog",
        48:	"Depositing rime fog",
        51:	"Drizzle: Light",
        53: "Drizzle: Moderate",
        55:	"Drizzle: Dense intensity",
        56: "Freezing Drizzle: Light",
        57:	"Freezing Drizzle: Dense intensity",
        61: "Rain: Slight",
        63: "Rain: Moderate",
        65:	"Rain: Heavy intensity",
        66:	"Freezing Rain: Light",
        67: "Freezing Rain: Heavy intensity",
        71: "Snow fall: Slight",
        73: "Snow fall: Moderate",
        75: "Snow fall: Heavy intensity",
        77:	"Snow grains",
        80: "Rain showers: Slight",
        81: "Rain showers: Moderate",
        82:	"Rain showers: Violent",
        85:	"Snow showers: Slight",
        86:	"Snow showers: Heavy",
        95: "Thunderstorm: Slight",
        96: "Thunderstorm with slight hail",
        99: "Thunderstorm with heavy hail"
    }

    export let data
    let responseData

    $: setCityData(data)

	onMount(() => interval = setInterval(loadCityData, 10 * 60 * 1000))
    onDestroy(() => clearInterval(interval))

    function setCityData(data) {
        data = data
        loadCityData()
    }

    function loadCityData() {
        const url = urlBuilder(data.latitude, data.longitude)
        axios.get(url)
        .then(function (response) {
            console.log(response.data);
            responseData = response.data
        })
        .catch(function (error) {
            console.error(error)
        })
    }

    function remove() {
        clearInterval(interval)
        dispatch('message', {
			text: 'remove'
		});
    }

    /*
    function getLocalTime(timezone) {
        let time = ""
        timezones.forEach(item => {
            let found = false
            item.utc.forEach(text => {
                if (timezone == text) {
                    found = true
                }
            })
            if (found == true) {
                time = item.offset
            }
        })
        return time
    }
    */

function urlBuilder(lat, lng) {
    const url = "https://api.open-meteo.com/v1/forecast"
    let params = "?latitude="+lat+"&longitude="+lng
    //params += "&hourly=temperature_2m,relativehumidity_2m,rain&current_weather=true&windspeed_unit=ms"
    params += "&current_weather=true&windspeed_unit=ms"
    return url + params
}

</script>
<main>
    <div style="border: solid 1px #000000; background-color: #ffff99; border-radius: 6px; width: 300px; height: 130px; padding: 4px;">
        <div style="display: flex; justify-content: space-between;">
            <div style="display: flex;">
                <!-- svelte-ignore missing-declaration -->
                <!-- svelte-ignore a11y-missing-attribute -->
                <img src={"./flags/" + data.country_code.toLowerCase() + ".svg"} style="margin: 2px;" />
                <span style="margin-top: 3px; margin-left: 2px; height: 20px;" class="line bold">{data.name}</span>
            </div>
            <button style="width: 20px; height: 20px; padding: 0;" on:click={remove}>X</button>
        </div>            
        {#if responseData != undefined}
            <div class="line bold">{data.country}</div>
            <div class="line">Timezone: {data.timezone}</div>
            <!-- div class="line">Local time: {getLocalTime(data.timezone)}</div -->
            <div class="line">Temperature: {responseData.current_weather.temperature} °C</div>
            <div class="line">Windspeed: {responseData.current_weather.windspeed} m/s</div>
            <div class="line">Winddirection: {responseData.current_weather.winddirection} degrees</div>
            <div class="line">{weatherCodes[responseData.current_weather.weathercode]}</div>
        {/if}
    </div>
</main>

<style>
    .bold {
        font-weight: bold;
    }
    .line {
        color:black;
        font-family: Verdana, Geneva, Tahoma, sans-serif;
        font-size: 14px;
    }
    img {
        width: 20px;
        height: 20px;
    }

</style>