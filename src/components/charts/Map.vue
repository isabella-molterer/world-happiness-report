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
                <input class="slider__input" type="range" min="0" max="4" step="1" v-bind:value="0" v-on:input="onChg($event)">
                <p class="slider__value">Year&nbsp;<span id="rangeValue">2015</span></p>               
            </div>
        </div>

        <!-- Chart -->
        <div id="svg_map">
            <svg id="map" width="100%" height="80vh" viewBox="250 -200 500 650"></svg>
        </div>

	</div>
</template>

<script>
    import '../../assets/css/chart.css'
    import * as d3 from 'd3'
    import * as topojson from "topojson-client"
    import data15 from '../data/2015.json'
    import data16 from '../data/2016.json'
    import data17 from '../data/2017.json'
    import data18 from '../data/2018.json'
    import data19 from '../data/2019.json'

    let idx = 0;

    export default {
        name: 'Map',
        data()  {
            return {
                countryData: [ data15, data16, data17, data18, data19 ]
            }
        },
        mounted() {
            this.drawMap(idx);
        },
        methods: {
            setColorScale() {
                let minMaxRank = this.getMinMax('Rank');
                const log = d3.scaleLog()
                    .domain([0, minMaxRank.min, minMaxRank.max])
                    .range(["#fff", "#54b3f8", "#c70000"]);

                var svg = d3.select("svg");

                svg.append("g")
                    .attr("class", "legendLog")
                    .attr("transform", "translate(20,20)");             
            },
            onChg(event) {
                idx = event.target.value;
                let time = ["2015", "2016", "2017", "2018", "2019"];
                document.getElementById("rangeValue").innerHTML = time[idx];
                this.drawMap(idx);
            },
            getMinMax(property) {
                let max = d3.max(this.countryData[idx], (d) => +d[property]); // +d = cast to number
                let min = d3.min(this.countryData[idx], (d) => +d[property]);
                return { min, max }
            },
            countryColorScale() {
                let minMaxRank = this.getMinMax('Rank');
                return d3.scaleLinear()
                    .domain([0, minMaxRank.min, minMaxRank.max])
                    .range(["#fff", "#54b3f8", "#c70000"]);
            },
            getCountryRank(name, idx) {
                let country = this.countryData[idx].filter(item => item.Country === name);
                return country.length ? country[0].Rank : "N/A";
            },
            getCountryScore(name, idx) {
                let country = this.countryData[idx].filter(item => item.Country === name);
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
                let infobox = document.getElementById("data__country");
                d3.json("https://unpkg.com/world-atlas@2.0.2/countries-110m.json")
                .then((world) => {
                    let svg = d3.select("#map");
                    svg.selectAll("*").remove();
                    svg.attr("fill", "#fff");
                    let path = d3.geoPath().projection(d3.geoMercator());
                    let scale = this.countryColorScale();
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
                            let rank = this.getCountryRank(name, idx);
                            let score = this.getCountryScore(name, idx);
                            infobox.innerHTML = `<p><span>Country:</span> ${name}</p><p><span>Rank:</span> ${rank}</p><p><span>Score:</span> ${score}</p>`
                        })
                        .on("mouseout", (event) => {
                            const id = this.getPathId(event.target);
                            d3.select(".path_" + id).attr("stroke", 'none');
                            infobox.innerHTML = `<p><span>Country:</span> -</p><p><span>Rank:</span> -</p><p><span>Score:</span> -</p>`
                        })
                        .attr("d", path)
                        .style("fill", (d) => {
                            let { name } = d.properties;
                            let rank = this.getCountryRank(name, idx);
                            return scale(rank);
                        });
                });
            }
        }
    };
</script>