<script>
  // Write your JS here, or import other files
  import * as d3 from 'd3';

  d3.csv('ghg-emissions-by-sector.csv', function(error, vCsvData) {
        if (error) throw error;

        vData = d3.stratify()(vCsvData);
        drawViz(vData);
  });

</script>

<main>
  <h1>Svelte template</h1>

  <p>Write your HTML here</p>
</main>

<style>
  /* Write your CSS here */
</style>
