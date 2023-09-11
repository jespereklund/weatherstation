<script>
// @ts-nocheck

    import axios from "axios";

    let searchResults = []
    let searchText
    let listVisible = true
    let ignoreFirstResponse = false
    export let selectedCity = {}

    document.addEventListener("click", cancel)

    document.onkeydown = function(evt) {
        evt = evt || window.event
        let isEscape = false
        if ("key" in evt) {
            isEscape = (evt.key === "Escape" || evt.key === "Esc")
        } else {
            isEscape = (evt.keyCode === 27)
        }
        if (isEscape) {
            cancel()
        }
    }    

    $: callCountryAPI(searchText)

    function callCountryAPI(searchText) {
        if (ignoreFirstResponse == false) {
            listVisible = true
        } 
        ignoreFirstResponse = false

        if ( searchText && 2 > searchText.length )
        {
            searchResults = []
            return
        }

        const url = "https://geocoding-api.open-meteo.com/v1/search?name=" + searchText
        axios.get(url)
        .then(function (response) {
            searchResults = response.data.results
        })
        .catch(function (error) {
        console.error(error)
        })
    }

    function addCity(item) {
        selectedCity = item
        console.log(selectedCity)
        searchText = ""
        listVisible = false
        ignoreFirstResponse = true
    }
    
    function cancel() {
        listVisible = false
        ignoreFirstResponse = true        
        searchText = ""
    }
</script>
<main>
    <div style="display: flex; gap: 2px">
        <div>
            <input type="text" bind:value={searchText} placeholder="Search for a city..." />
            {#if searchResults && listVisible}
                <div class="result-box">
                    <ul>
                        {#each searchResults as result}
                            {#if result.country_code != undefined && result.name != undefined && result.admin1 != undefined && result.country != undefined && result.latitude != undefined && result.longitude != undefined } 
                                <li>
                                    <!-- svelte-ignore a11y-click-events-have-key-events -->
                                    <div on:click={addCity(result)} style="display:flex; align-items: center; cursor: pointer;">
                                        <!-- svelte-ignore a11y-missing-attribute -->
                                        <img src={"./flags/" + result.country_code.toLowerCase() + ".svg"} style="margin: 2px;" />
                                        <!-- svelte-ignore a11y-invalid-attribute -->
                                        <div style="color: black; font-family:Verdana, Geneva, Tahoma, sans-serif; font-size: 14px;">{result.name} {result.admin1 ?? ''} {result.country} ({result.latitude}, {result.longitude})</div>
                                    </div>
                                </li>                
                            {/if}
                        {/each}
                    </ul>
                </div>
            {/if}
        </div>            
    </div>
</main>
<style>
    input[type=text] {
        width: 600px;
    }

    .result-box {
        width: 605px;
        border: 1px solid #000000;
        border-radius: 0;
        background-color: #ffffff;
    }
    img {
        width: 20px;
        height: 20px;
    }

    a {
        text-decoration: none;
        color: #000000;
    }

    ul {
        list-style-type: none;
        padding: 0px;
        margin: 0px;
    }
</style>