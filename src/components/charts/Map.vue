<template>
	<div class="map has-backgound-light">
        <!-- User Input -->
        <div id="map__data" class="data">
            <div id="data__country" class="data__country">
                <p><span>Country:</span> -</p>
                <p><span>Rank:</span> -</p>
                <p><span>Score:</span> -</p>
            </div>
            <div class="data__slider slider">
                <input class="slider__input" type="range" min="0" max="7" step="1" v-bind:value="0" v-on:input="onChg($event)">
                <p class="slider__value">Year&nbsp;<span id="rangeValue">2015</span></p>             
            </div>
        </div>

        <!-- Heat Map -->
        <div id="svg_map">
            <svg id="map" viewBox="0 -150 1000 600" fill="#fff" preserveAspectRatio="none"></svg>
        </div>
	</div>
</template>

<script>
    import * as d3 from 'd3'
    import * as topojson from "topojson-client"
    import data15 from '../../assets/data/2015.json'
    import data16 from '../../assets/data/2016.json'
    import data17 from '../../assets/data/2017.json'
    import data18 from '../../assets/data/2018.json'
    import data19 from '../../assets/data/2019.json'
    import data20 from '../../assets/data/2020.json'
    import data21 from '../../assets/data/2021.json'
    import data22 from '../../assets/data/2022.json'

    let idx = 0;

    export default {
        name: 'Map',
        data()  {
            return {
                countryData: [ data15, data16, data17, data18, data19, data20, data21, data22 ]
            }
        },
        mounted() {
            this.drawMap(idx);
        },
        methods: {
            setColorScale() {
                let svg = d3.select("svg");

                svg.append("g")
                    .attr("class", "legendLog")
                    .attr("transform", "translate(20,20)");             
            },
            onChg(event) {
                idx = event.target.value;
                const time = ["2015", "2016", "2017", "2018", "2019", "2020", "2021", "2022"];
                document.getElementById("rangeValue").innerHTML = time[idx];
                this.drawMap(idx);
            },
            getMinMax(property) {
                const max = d3.max(this.countryData[idx], (d) => +d[property]); // +d = cast to number
                const min = d3.min(this.countryData[idx], (d) => +d[property]);
                return { min, max }
            },
            countryColorScale() {
                const minMaxRank = this.getMinMax('Rank');
                return d3.scaleLinear()
                    .domain([0, minMaxRank.min, minMaxRank.max])
                    .range(["#fff", "#54b3f8", "#c70000"]);
            },
            getCountryRank(name, idx) {
                const country = this.countryData[idx].filter(item => item.Country === name);
                return country.length ? country[0].Rank : "N/A";
            },
            getCountryScore(name, idx) {
                const country = this.countryData[idx].filter(item => item.Country === name);
                return country.length ? country[0].Score : "N/A";
            },
            getPathId(target) {
                let targetPath = null;
                const targetClass = target.classList[0];

                if (targetClass) {
                    targetPath = targetClass.split('_')[1];
                }
                
                return targetPath;
            },
            drawMap(idx) {
                const infobox = document.getElementById("data__country");
                d3.json("https://unpkg.com/world-atlas@2.0.2/countries-110m.json")
                .then((world) => {
                    let svg = d3.select("#map");
                    svg.selectAll("*").remove();
                    svg.attr("fill", "#fff");
                    const path = d3.geoPath().projection(d3.geoMercator());
                    const scale = this.countryColorScale();
                    var mainGroup = svg.append("g");
                    mainGroup.selectAll("path")
                        .data(topojson.feature(world,world.objects.countries).features)
                        .enter()
                        .append("path")
                        .attr("class", (d, i) => {
                            return "path_" + i
                        })
                        .on("mouseover", (event, data) => {
                            const name = data.properties.name;
                            const id = this.getPathId(event.target);
                            d3.select(".path_" + id).attr("stroke", 'white').attr("stroke-width", '1.5');
                            d3.select(".path_" + id).style("cursor", "pointer");
                            const rank = this.getCountryRank(name, idx);
                            const score = this.getCountryScore(name, idx);
                            infobox.innerHTML = `<p><span>Country:</span> ${name}</p><p><span>Rank:</span> ${rank}</p><p><span>Score:</span> ${score}</p>`
                        })
                        .on("mouseout", (event) => {
                            const id = this.getPathId(event.target);
                            d3.select(".path_" + id).attr("stroke", 'none');
                            infobox.innerHTML = `<p><span>Country:</span> -</p><p><span>Rank:</span> -</p><p><span>Score:</span> -</p>`
                        })
                        .attr("d", path)
                        .style("fill", (d) => {
                            const { name } = d.properties;
                            const rank = this.getCountryRank(name, idx);
                            return scale(rank);
                        });
                });
            }
        }
    };
</script>