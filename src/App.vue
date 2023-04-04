<template>
  <div id="app">
    <TheHeader />

    <div class="container content">
      <h1>World Happiness Report</h1>
      <div class="intro">
        <p>The World Happiness Report is an annual publication that aims to measure and compare the level of happiness in different countries around the world. 
          To do this, the report uses data from the Gallup World Poll, which is a large-scale survey. 
          The happiness scores and rankings in the World Happiness Report are based on a single question that asks respondents to evaluate their own lives on a scale of 0 to 10. 
          This question is known as the Cantril ladder, and it asks people to imagine a ladder with the best possible life being at the top (10) and the worst possible life at the bottom (0). 
          Respondents are then asked to rate their current life on that scale.
        </p>
        <p>
          The scores used in the report are from nationally representative samples of the population in each country. These samples are taken from the years 2015-2022 and are weighted using the Gallup weights to make the estimates representative.
        </p>

        <p>The report also looks at six factors that contribute to the happiness score: GDP per capita (GDP), Social support (Family), Healthy Life Expectancy (Health), 
          Freedom to make life choices (Freedom), Generosity (Generosity) and Corruption Perception (Trust). 
          These factors are compared to the values of a hypothetical country called Dystopia, which has the world's lowest national averages for each factor. While these factors don't affect a country's total happiness score, they help explain why some countries rank higher than others.
          Overall, the scores and rankings in the World Happiness Report provide insights into how people in different countries perceive their lives and how factors such as economic, social, and political conditions contribute to their overall sense of well-being.
        </p>
        <p>
          Data source: <a target="_blank" href="https://worldhappiness.report/">https://worldhappiness.report/</a><br>
          Source code: <a target="_blank" href="https://github.com/isabella-molterer/world-happiness-report">https://github.com/isabella-molterer/world-happiness-report</a>
        </p>
        <p>Technologies used: 
          <a target="_blank" href="https://vuejs.org/">Vue.js</a>,
          <a target="_blank" href="https://d3js.org/">D3.js</a> and 
          <a target="_blank" href="https://www.chartjs.org/">Chart.js</a>. 
        </p>
      </div>

      <!-- World map visualisation -->
      <section id="map-visualisation">
        <h2>Visualisation as heat map</h2>
        <p>This map is a visual representation of the World Happiness Report's rankings from 2015 to 2022, shown as a heat map.</p>
        <p>The color and intensity of the colors on the map reflects the level of happiness in each country.
          Countries with cooler colors, such as blue and green, are ranked higher and have higher happiness scores, while countries with warmer colors, such as red and orange, are ranked lower and have lower happiness scores.
          If a country is filled with white, it means that it did not participate in the survey in that particular year, so there is no data available to determine its happiness score. </p>
        <p>
          Overall, this heat map provides an easy-to-understand visual summary of how happiness levels have changed across the world over time.
        </p>
        <Map/>
      </section>


      <!-- Radar plot visualisation -->
      <section id="radar-chart-visualisation">
        <h2>Visualisation as radar chart</h2>
        <div class="intro">
          <p>This is a radar chart provides an interactive and visual way to compare the happiness levels of two countries by exploring six different metrics: GDP, Family, Health, Freedom, Trust, and Generosity.</p>
          <p>To interact with the chart, you can first (1) select a country, which finds the country that comes closest to your personal happiness score. 
            Then, you can (2) choose another country from the dropdown menu to compare it with the first one.<br>
            Please note that your personal happiness score can only range between the highest and lowest ranked countries, so you cannot choose a score outside of these limits.
          </p>
          <p>The default setting is to compare the happiest country of 2022, Finland, with the unhappiest country, Afghanistan.</p>
        </div>
        <Radar/>
      </section>

      <!-- Scatter plot visualisation -->
      <section id="scatter-plot-visualisation">
        <h2>Visualisation as scatter plot</h2>
        <p>This scatter plot shows how different regions in the world are affected by the six different factors that contribute to happiness: GDP, Family, Health, Freedom, Trust, and Generosity. 
          The chart is divided into 10 different regions based on the World Happiness Report of 2022.</p>
        <p>By interacting with the chart, you can explore the correlation between the average values of these six metrics as well as the rank of each region. 
          The scatter plot provides a visual representation of the data, where each region is represented by a dot on the chart, and the position of the dot shows how the region is ranked in terms of happiness score and the six metrics.
        </p>
        <p>This chart allows you to easily compare the happiness levels of different regions in the world and identify patterns and trends in the data.</p>
        <Scatter/>
      </section>
    </div>

    <TheFooter />
  </div>
</template>

<script>
  import Map from "./components/charts/Map.vue";
  import Radar from "./components/charts/Radar.vue";
  import Scatter from "./components/charts/Scatter.vue";
  import TheHeader from "./components/TheHeader.vue";
  import TheFooter from "./components/TheFooter.vue";

  export default {
    name: "app",
    components: {
      Radar, Map, Scatter, TheHeader, TheFooter,
    },
  };
</script>

<style lang="scss">
@import "./assets/styles/styles.scss";
</style>
