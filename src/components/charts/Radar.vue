<template>
	<div class="radarplot has-backgound-light">
		<!-- User Input -->
		<div class="radarplot__input">
			<!-- Range Slider: Personal Score -->
			<div class="range__slider-wrapper">
				<p class="label" id="info">1) Insert your personal happiness score!</p>
				<div class="range__slider-wrapper slider-wrapper">
                    <span alt="Sad Smiley">&#128577;</span>
                    <div class="range__slider slider">
                        <input class="slider__input" type="range" step="1"  min="0" :max="maxRank" v-model="currentValue" v-on:input="onChangeSlider($event)"/>
                        <p class="slider__value">Score:&nbsp;<span id="rangeValue">{{ currentScore }}</span></p>      
                    </div>
                    <span alt="Happy Smiley">&#128578;</span>
				</div>
			</div>

			<!-- Dropdown: Comparison Country -->
			<div class="select">
				<p class="label" id="message">2) Compare to another country!</p>
				<select @change="onChangeDropdwn($event)" id="selectButton" class="select__dropdown"></select>
			</div>
		</div>

		<!-- Chart -->
		<div class="radarplot__plot-wrapper">
			<canvas id="radarplot" width="400" height="400"></canvas>
		</div>
	</div>

</template>

<script>
    import * as d3 from 'd3'
    import data from '../../assets/data/2022.json'
    import Chart from 'chart.js';

    let chart;

    export default {
        name: 'Radar',
        data()  {
            return {
                data: data,
                updateValue: (d3.min(data, (d) => +d['Rank'])),
                maxRank: (data.filter((item) => item.Score !== 'N/A').length),
                currentScore: '---',
                currentValue: (d3.max(data, (d) => +d['Rank']))
            };
        },
        mounted() {
            this.createDropdown(this.data);
            let options = document.getElementById("selectButton").getElementsByTagName("option");
            for (let option of options) {
                if (option.innerText === "Afghanistan") {
                    option.selected = 'selected';
                }
            }
            const ctx = document.getElementById('radarplot').getContext('2d');
            
            // default countries
            const country1 = this.data.filter((item) => item.Score == this.getMinMax('Score').max)[0]; // happiest country
            const country2 = this.data.filter((item) => item.Score == this.getMinMax('Score').min)[0]; // unhappiest country
            this.currentScore = country1.Score;

            Chart.defaults.global.defaultFontFamily = "Lato";
            chart = new Chart(ctx, {
                label: 'Influence of different metrics',
                type: 'radar',
                data: {
                    labels: ['GDP', 'Family', 'Health', 'Freedom', 'Trust', 'Generosity'],
                    datasets: [
                        this.createDatasets(country1, '59, 124, 171', '#89d8fa'),
                        this.createDatasets(country2, '163, 0, 0', '#ff1a1a')
                    ],
                },
                options: this.createOptions()
            });
        },
        methods: {
            createDropdown() {
                let allCountries = [];
                for (let entry in this.data) {
                    allCountries.push(this.data[entry].Country);
                }
                d3.select("#selectButton")
                    .selectAll('myOptions')
                    .data(allCountries)
                    .enter()
                    .append('option')
                    .text(function (d) { return d; })
            },
            onChangeSlider(event) {
                const idx = event.target.value;
                let allRanks = [];
                this.data.filter((item) => {
                    if (item.Rank !== 'N/A') {
                        allRanks.push(item.Rank);
                    }
                });
                allRanks.sort(function(a, b){return b - a});
                this.updateValue = allRanks[idx];
                this.updateRadarCountry1(allRanks[idx]);
                
                const currentScore = this.data.filter((item) => item.Rank === allRanks[idx])[0].Score;
                this.currentScore = currentScore;    
            },
            closestRank(score, ranks) {
                let currentRank = ranks[0];
                for (let rank of ranks) {
                    if (Math.abs(score - rank) < Math.abs(score - currentRank)) {
                        currentRank = rank
                    }
                }
                return currentRank
            },
            updateRadarCountry1(rank) {
                let message = document.getElementById('info');
                message.innerHTML = '';
                let allRanks = [];
                this.data.filter((item) => {
                    if (item.Rank !== 'N/A') {
                        allRanks.push(item.Rank);
                    }
                });
                const closest = this.closestRank((rank), allRanks.sort());
                const country = this.data.filter((item) => item.Rank === closest)[0];

                message.innerHTML = '1) Your score resembles ' + country.Country + ' the most!';

                chart.data.datasets[0].data = [country.GDP, country.Family, country.Health, country.Freedom, country.Trust, country.Generosity];
                chart.data.datasets[0].label = country.Country + ' (Rank: ' + country.Rank +')';
                chart.update();
            },
            onChangeDropdwn(e) {
                this.updateRadarCountry2(e.target.value);
            },
            updateRadarCountry2(name) {
                let warning = document.getElementById('message');
                warning.innerHTML = '';
                warning.classList.remove('alert');
                const country = this.data.filter(item => item.Country === name)[0];
                if (country.Rank === "N/A") {
                    warning.innerHTML = country.Country + ' did not take part in the survey of 2019!';
                    warning.classList.add('alert');
                } else {
                    warning.innerHTML = '2) Country of comparison: ' + country.Country + ' (Score:' + country.Score + ')';
                }
                chart.data.datasets[1].data = [country.GDP, country.Family, country.Health, country.Freedom, country.Trust, country.Generosity];
                chart.data.datasets[1].label = country.Country + ' (Rank: ' + country.Rank +')';
                chart.update();
            },
            createDatasets(country, color, hovercolor) {
                return {
                    backgroundColor: 'rgba(' + color + ', 0.3)',
                    label: country.Country + ' (Rank: ' + country.Rank +')',
                    hoverBorderColor: hovercolor,
                    borderColor: 'rgba(' + color + ')',
                    hoverBackgroundColor: hovercolor,
                    pointHitRadius: 3,
                    data: [country.GDP, country.Family, country.Health, country.Freedom, country.Trust, country.Generosity]
                }
            },
            createOptions() {
                return {
                    tooltips: {
                        titleSpacing: 10,
                        titleFontSize: 16,
                        bodyFontSize: 14,
                        bodySpacing: 10,
                        footerSpacing: 5
                    },
                    legend: {
                        labels: {
                            fontColor: 'rgba(255, 255, 255, 1',
                            fontSize: 20
                        }
                    },
                    scale: {
                        gridLines: {
                            color: 'rgba(255, 255, 255, 0.1'
                        },
                        angleLines: {
                            color: 'rgba(255, 255, 255, 0.4'
                        },
                        ticks: {
                            suggestedMin: 0,
                            suggestedMax: this.getMaxMetric()
                        },
                        pointLabels: {
                            lineHeight: 3,
                            fontColor: 'rgba(255, 255, 255, 1',
                            fontSize: 18
                        }
                    }
                }
            },
            getMaxMetric() {
                let maxMetrics = [];
                let labels = ['GDP', 'Family', 'Health', 'Freedom', 'Trust', 'Generosity'];

                for (let metric in labels) {
                    maxMetrics.push(d3.max(this.data, (d) => +d[labels[metric]]));
                }
                return Math.max(...maxMetrics)
            },
            getMinMax(property) {
                let max = d3.max(this.data, (d) => +d[property]); // +d = cast to number
                let min = d3.min(this.data, (d) => +d[property]);
                return { min, max }
            }
        }
    }
</script>
