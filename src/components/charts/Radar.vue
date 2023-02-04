<template>
	<div>
		<h2>How Happy are You?</h2>
		<div class="intro">
			<p>In the example below you can compare two countries and explore how the six different metrics GDP, Family, Health, Freedom, Trust
				and Generosity contribute to their happiness.</p>
			<p>	First you can set the first country by choosing a value on the slider to find out which country comes closest to your personal
				happiness score! Afterwards you can choose any other country in the dropdown menu of step 2) to compare.</p>
			<p>To start with, we assumed you probably want to check out the happiest (Finland) and unhappiest country (South Sudan) of year the 2019.</p>
		</div>

		<!-- User Input -->
		<div id="user_input">
			<!-- Slider: Personal Score -->
			<div class="steps">
				<p class="label" id="info">1) Insert your personal happiness score!</p>
				<div class="range-slider_wrapper">
					<img src="../../assets/svg/sad.svg" alt="Sad Smiley">
					<input type="range" step="1" class="range-slider" min="0" v-bind:max="my_max" v-model="value" v-on:input="onChangeSldr($event)"/>
					<img src="../../assets/svg/happy.svg" alt="Happy Smiley">
				</div>
			</div>

			<!-- Dropdown: Comparison Country -->
			<div class="steps">
				<p class="label" id="message">2) Compare to another country!</p>
				<select @change="onChangeDropdwn($event)" id="selectButton" class="dropdown-selection"></select>
			</div>

		</div>

		<!-- Chart -->
		<div id="chart-container">
			<canvas id="myChart" width="400" height="400"></canvas>
		</div>
	</div>

</template>

<script>
    import * as d3 from 'd3'
    import '../../assets/css/chart.css'
    import data19 from '../data/2019.json'
    import Chart from 'chart.js';

    let chart;

    export default {
        name: 'Radar',
        data()  {
            return {
                data: data19,
                cur_val: (d3.min(data19, (d) => +d['Rank'])),
                my_max: (data19.filter((item) => item.Score !== 'N/A').length - 1), // 154
                value: (d3.max(data19, (d) => +d['Rank']))
            };
        },
        mounted() {
            this.createDropdown(this.data);
            let options = document.getElementById("selectButton").getElementsByTagName("option");
            for (let opt of options) {
                if (opt.innerText === "S. Sudan") {
                    opt.selected = 'selected';
                }
            }
            var ctx = document.getElementById('myChart').getContext('2d');

            // default countries
            let ctry1 = this.data.filter((item) => item.Score === this.getMinMax('Score').max)[0]; // happiest country
            let ctry2 = this.data.filter((item) => item.Score === this.getMinMax('Score').min)[0]; // unhappiest country

            Chart.defaults.global.defaultFontFamily = "Lato";
            chart = new Chart(ctx, {
                label: 'Influence of different metrics',
                type: 'radar',
                data: {
                    labels: ['GDP', 'Family', 'Health', 'Freedom', 'Trust', 'Generosity'],
                    datasets: [
                        this.createDatasets(ctry1, '59, 124, 171', '#89d8fa'),
                        this.createDatasets(ctry2, '163, 0, 0', '#ff1a1a')
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
            onChangeSldr(event) {
                let idx = event.target.value;
                let allRanks = [];
                this.data.filter((item) => {
                    if (item.Rank !== 'N/A') {
                        allRanks.push(item.Rank);
                    }
                });
                allRanks.sort(function(a, b){return b - a});
                this.cur_val = allRanks[idx];
                this.updateRadarCtry1(allRanks[idx]);
            },
            closestValue(score, arr) {
                let curr = arr[0];
                for (let val of arr) {
                    if (Math.abs(score - val) < Math.abs(score - curr)) {
                        curr = val
                    }
                }
                return curr
            },
            updateRadarCtry1(rank) {
                let message = document.getElementById('info');
                message.innerHTML = '';
                let allRanks = [];
                this.data.filter((item) => {
                    if (item.Rank !== 'N/A') {
                        allRanks.push(item.Rank);
                    }
                });
                let closest = this.closestValue((rank), allRanks.sort());
                let ctry = this.data.filter((item) => item.Rank === closest)[0];

                message.innerHTML = '1) Your score resembles ' + ctry.Country + ' the most!';

                chart.data.datasets[0].data = [ctry.GDP, ctry.Family, ctry.Health, ctry.Freedom, ctry.Trust, ctry.Generosity];
                chart.data.datasets[0].label = ctry.Country + ' (Rank: ' + ctry.Rank +')';
                chart.update();
            },
            onChangeDropdwn(e) {
                let selectedOption = e.target.value;
                this.updateRadarCtry2(selectedOption);
            },
            updateRadarCtry2(name) {
                let warning = document.getElementById('message');
                warning.innerHTML = '';
                warning.classList.remove('alert');
                let ctry = this.data.filter(item => item.Country === name)[0];
                if (ctry.Rank === "N/A") {
                    warning.innerHTML = ctry.Country + ' did not take part in the survey of 2019!';
                    warning.classList.add('alert');
                } else {
                    warning.innerHTML = '2) You chose ' + ctry.Country;
                }
                chart.data.datasets[1].data = [ctry.GDP, ctry.Family, ctry.Health, ctry.Freedom, ctry.Trust, ctry.Generosity];
                chart.data.datasets[1].label = ctry.Country + ' (Rank: ' + ctry.Rank +')';
                chart.update();
            },
            createDatasets(ctry, color, hovercolor) {
                return {
                    backgroundColor: 'rgba(' + color + ', 0.3)',
                    label: ctry.Country + ' (Rank: ' + ctry.Rank +')',
                    hoverBorderColor: hovercolor,
                    borderColor: 'rgba(' + color + ')',
                    hoverBackgroundColor: hovercolor,
                    pointHitRadius: 3,
                    data: [ctry.GDP, ctry.Family, ctry.Health, ctry.Freedom, ctry.Trust, ctry.Generosity]
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
                let metrics = ['GDP', 'Family', 'Health', 'Freedom', 'Trust', 'Generosity'];
                let max_vals = [];
                for (let m in metrics) {
                    max_vals.push(d3.max(this.data, (d) => +d[metrics[m]]));
                }
                return Math.max(...max_vals)
            },
            getMinMax(property) {
                let max = d3.max(this.data, (d) => +d[property]); // +d = cast to number
                let min = d3.min(this.data, (d) => +d[property]);
                return { min, max }
            }
        }
    }
</script>

<style scoped>
	#message.alert {
		color: #c70000;
		font-weight: bolder;
	}

	#score_range {
		display: block;
		margin: 10px 0 50px 0;
	}

	#user_input {
		display: flex;
		width: 80vw;
		margin: 0 auto;
		flex-wrap: wrap;
	}

	#user_input .steps {
		flex-basis: 100%;
		justify-content: space-evenly;
		flex-direction: column;
	}

	@media (min-width:1200px) {
		#user_input {
			flex-wrap: nowrap;
			width: 50vw;
		}

		#user_input .steps {
			margin: 0 auto;
			width: 100%;
			justify-content: space-evenly;
			flex-direction: row;
			text-align: center;
		}
	}
</style>