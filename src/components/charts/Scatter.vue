<template>
    <div>
        <h2>What influences the Happiness of the Regions?</h2>
        <div class="intro">
            <p>In the following scatter chart you can find out a bit more about the different regions in the world.
                In the happiness report, the world got divided in 10 different regions.
                You can explore the correlation between the average values of the 6 different metrics
                (GDP, Family, Health, Freedom, Trust and Generosity),
                as well as the rank and the score of the different regions. </p>
        </div>
        <!-- User Input -->
        <div id="user_input">
            <!-- X Metric -->
            <div class="steps">
                <p class="label">X:</p>
                <select @change="onXchange($event)" id="x-select" class="dropdown-selection"></select>
            </div>

            <!-- Y Metric -->
            <div class="steps">
                <p class="label">Y:</p>
                <select @change="onYchange($event)" id="y-select" class="dropdown-selection"></select>
            </div>

        </div>
        <!-- Legend -->
        <div class="legend">
            <ul>
                <li>Australia and New Zealand</li>
                <li>Western Europe</li>
                <li>Eastern Asia</li>
                <li>Southeastern Asia</li>
                <li>Southern Asia</li>
                <li>North America</li>
                <li>Latin America and Caribbean</li>
                <li>Central and Eastern Europe</li>
                <li>Middle East and Northern Africa</li>
                <li>Sub-Saharan Africa</li>
            </ul>
        </div>
        <!-- Chart -->
        <div id="chart-container">
            <canvas id="scatter" width="400" height="400"></canvas>
        </div>
    </div>
</template>

<script>

    import * as d3 from 'd3'
    import '../../assets/css/chart.css'
    import data_regions from '../data/region_means.json'
    import Chart from 'chart.js';

    const arrayColumn = (arr, n) => arr.map(x => x[n]);

    let chart;
    let options = ['Rank', 'Score', 'GDP', 'Family', 'Health', 'Freedom', 'Trust', 'Generosity'];
    let regions = arrayColumn(data_regions, 'Region');
    var x = 'Rank';
    var y = 'Rank';

    export default {
        name: 'Scatter',
        data() {
            return {
                data: data_regions
            };
        },
        mounted() {
            this.createDropdown();
            var ctx = document.getElementById('scatter').getContext('2d');


            var pointBackgroundColors = ["#a6cee3", "#1f78b4",
                "#b2df8a", "#33a02c",
                "#fb9a99", "#e31a1c",
                "#fdbf6f", "#ff7f00",
                "#cab2d6", "#6a3d9a"];

            Chart.defaults.global.defaultFontFamily = "Lato";
            chart = new Chart(ctx, {
                type: 'scatter',

                data: {
                    datasets: [{
                        data: this.generateData(),
                        label: 'Regions',
                        pointBackgroundColor: pointBackgroundColors,
                        hoverBorderColor: '#ff1a1a',
                        borderColor: '#fff',
                        hoverBackgroundColor: 'red',
                        pointHitRadius: 3,
                        pointHoverRadius: 5,
                        radius: 7
                    }]
                },
                options: this.createOptions()
            });
            chart.update();
        },
        methods: {
            updateChart(new_data) {
                chart.data.datasets[0].data = new_data;
                chart.update();
            },
            generateData(new_x = x, new_y = y) {
                x = new_x;
                y = new_y;
                var data = [];
                for (var i = 0; i < data_regions.length; i++) {
                    data.push({
                        index: data_regions[i].Region,
                        x:  data_regions[i][x],
                        y:  data_regions[i][y],
                    });
                }
                return data;
            },
            createDropdown() {
                d3.select("#x-select")
                    .selectAll('myOptions')
                    .data(options)
                    .enter()
                    .append('option')
                    .text(function (d) { return d; }) // text showed in the menu
                d3.select("#y-select")
                    .selectAll('myOptions')
                    .data(options)
                    .enter()
                    .append('option')
                    .text(function (d) { return d; }) // text showed in the menu
            },
            onXchange(e) {
                let new_value = e.target.value;
                var new_data = this.generateData(new_value, y);
                chart.options.scales.xAxes[0].scaleLabel.labelString = new_value;
                this.updateChart(new_data);
            },
            onYchange(e) {
                let new_value = e.target.value;
                var new_data = this.generateData(x, new_value);
                chart.options.scales.yAxes[0].scaleLabel.labelString = new_value;
                this.updateChart(new_data);
            },
            createOptions() {
                return {
                    tooltips: {
                        titleSpacing: 10,
                        titleFontSize: 16,
                        bodyFontSize: 14,
                        bodySpacing: 10,
                        footerSpacing: 5,
                        callbacks: {
                            label: function(tooltipItem) {
                                var label = regions[tooltipItem.index];
                                return label + ' ('+ x +' '+ tooltipItem.xLabel + ', '+ y +' '+ tooltipItem.yLabel + ')';
                            }
                        }
                    },
                    legend: {
                        display: false,
                        //position: 'right',
                        labels: {
                            fontColor: 'rgba(255, 255, 255, 1',
                            fontSize: 20
                        }
                    },
                    scales: {
                        xAxes: [{
                            type: 'linear',
                            position: 'bottom',
                            scaleLabel: {
                                display: true,
                                labelString: x,
                                lineHeight: 3,
                                fontColor: 'rgba(255, 255, 255, 1',
                                fontSize: 18
                            },
                            gridLines: {
                                color: 'rgba(255, 255, 255, 0.1'
                            },
                            ticks: {
                                fontSize: 12
                            }
                        }],
                        yAxes: [{
                            scaleLabel: {
                                display: true,
                                labelString: y,
                                lineHeight: 3,
                                fontColor: 'rgba(255, 255, 255, 1',
                                fontSize: 18
                            },
                            gridLines: {
                                color: 'rgba(255, 255, 255, 0.1'
                            },
                        }]
                    }
                }
            }
        }
    }

</script>

<style scoped>
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