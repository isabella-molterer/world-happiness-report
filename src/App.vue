<template>
  <div id="app">
    <h1>World Happiness Report</h1>
    <div class="intro">
      <p>The happiness scores and rankings use data from the Gallup World Poll. The scores are based on answers to the main life evaluation question asked in the poll.
        This question, known as the Cantril ladder, asks respondents to think of a ladder with the best possible life for them being a 10 and the worst possible life being a 0
        and to rate their own current lives on that scale.
        The scores are from nationally representative samples for the years 2015-2019 and use the Gallup weights to make the estimates representative.</p>
      <p>The columns following the happiness score estimate the extent to which each of six factors – economic production, social support, life expectancy, freedom, absence of corruption, and generosity –
        contribute to making life evaluations higher in each country than they are in Dystopia, a hypothetical country that has values equal to the world’s lowest national averages for each of the six factors.
        They have no impact on the total score reported for each country, but they do explain why some countries rank higher than others.</p>
    </div>
    <Map/>
    <Radar/>
    <Scatter/>
    <div class="footer">
      <p>Source: <a target="_blank" href="https://worldhappiness.report/">https://worldhappiness.report/</a><br>
      Visualisation by Molterer Isabella & Eibl Lisa-Maria<br>
      The visualizations are made with D3, Vue and Chart.js</p>
    </div>
  </div>
</template>

<script>
  import Map from "./components/charts/Map.vue";
  import Radar from "./components/charts/Radar.vue";
  import Scatter from "./components/charts/Scatter.vue";
  import './assets/css/styles.css'

  export default {
    name: "app",
    components: {
      Radar, Map, Scatter
    },
    data: function() {
      return {
        loadData: []
      };
    },
    mounted() {
    },
    methods: {
    }
  };
</script>
