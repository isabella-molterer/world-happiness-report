<template>
  <div id="app">
    <TheHeader />

    <div class="container content">
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

      <!-- World map visualisation -->
      <section>
        <h2 id="map-visualisation">How Happy is the World?</h2>
        <p>On this map, you can see how the ranks and scores have changed between 2015 and 2019.
            The blacked out countries didn't participate in the survey in that particular year.</p>
        <Map/>
      </section>


      <!-- Radar plot visualisation -->
      <h2 id="radar-plot-visualisation">How Happy are You?</h2>
      <div class="intro">
        <p>In the example below you can compare two countries and explore how the six different metrics GDP, Family, Health, Freedom, Trust
          and Generosity contribute to their happiness.</p>
        <p>	First you can set the first country by choosing a value on the slider to find out which country comes closest to your personal
          happiness score! Afterwards you can choose any other country in the dropdown menu of step 2) to compare.</p>
        <p>To start with, we assumed you probably want to check out the happiest (Finland) and unhappiest country (South Sudan) of year the 2019.</p>
      </div>
      <Radar/>

      <!-- Scatter plot visualisation -->
      <h2 id="scatter-plot-visualisation">What influences the Happiness of the Regions?</h2>
      <p>In the following scatter chart you can find out a bit more about the different regions in the world.
          In the happiness report, the world got divided in 10 different regions.
          You can explore the correlation between the average values of the 6 different metrics
          (GDP, Family, Health, Freedom, Trust and Generosity),
          as well as the rank and the score of the different regions. </p>
      <Scatter/>
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
@import "./assets/styles/abstracts/variables";
@import "./assets/styles/styles.scss";
</style>
