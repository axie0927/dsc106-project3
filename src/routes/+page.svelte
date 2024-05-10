<script>
  import App from '../components/App.svelte';
</script>

<h1>Treemap of Greenhouse Gas Emissions by Country and Industrial Sector</h1>

<App />

<h3>Write-up: </h3>
<p> For our visualization, we decided to do a treemap to visualize greenhouse gas emissions for each country and grouped by industrial sector.</p>
<p> Our data set was obtained from https://ourworldindata.org/emissions-by-sector. We decided to use a tree map to show the size of the </p>
<p> greenhouse emissions for each country. For our data set, we had to use Pandas to reorganize our data so that it is in a hierarchal </p>
<p> structure where the parent variable is 'year', with its child being 'industrial sector' and lastly 'country' being the child of that. </p>
<p> Each country has a value for greenhouse emissions. </p>
