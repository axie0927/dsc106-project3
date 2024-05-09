<script>
  import { onMount } from 'svelte';
  import * as d3 from 'd3';


  let data;
  let CurrentYear = 1990;

  onMount(async () => {
      // Load data from the static folder
      const response = await fetch('country.json');
      const allData = await response.json();

      // Extract emissions data for the year 1990
      data = allData.find(item => item.year === CurrentYear);

      // Extract unique sectors and assign colors
      const sectors = data.children.map(d => d.name);
      const colorScale = d3.scaleOrdinal(d3.schemeCategory10)
                           .domain(sectors);
      console.log(d3.select('#treemap'));
      // Create treemap using D3
      const width = 1400;
      const height = 1000;

      const svg = d3.select('#treemap')
                    .attr('width', width)
                    .attr('height', height);

      const treemap = d3.treemap()
                        .size([width, height])
                        .padding(1)
                        .round(true);

      const root = d3.hierarchy(data)
                     .sum(d => d.value)
                     .sort((a, b) => b.value - a.value); // Sort sectors by emission value

      treemap(root);

      const Tooltip = d3.select('body')
                        .append('div')
                        .attr('class', 'tooltip')
                        .style('opacity', 0)
                        .attr("class", "tooltip")
                        .style("background-color", "white")
                        .style("border", "solid")
                        .style("border-width", "2px")
                        .style("border-radius", "5px")
                        .style("padding", "5px")
                        .style("position", "absolute");

      const mouseover = function(event, d) {
          Tooltip.style('opacity', 1);
          d3.select(this)
            .style('stroke', 'black')
            .style('opacity', 1);
      };

      const mousemove = function(event, d) {
          Tooltip.html(`Country: ${d.data.name}<br>Greenhouse Emission: ${d.value/1000000} Million Tons `)
                 .style('left', (event.x) + 20 + "px")
                 .style('top', (event.y) + "px")
      };

      const mouseleave = function(event, d) {
          Tooltip.style('opacity', 0);
          d3.select(this)
            .style('stroke', 'none')
            .style('opacity', 0.8);
      };

      svg.selectAll('rect')
         .data(root.leaves())
         .enter()
         .append('rect')
         .attr('x', d => d.x0)
         .attr('y', d => d.y0)
         .attr('width', d => d.x1 - d.x0)
         .attr('height', d => d.y1 - d.y0)
         .attr('fill', d => colorScale(d.parent.data.name))
         .style('stroke-width', 4)
         .style('stroke', 'none')
         .style('opacity', 0.8)
         .on('mouseover', mouseover)
         .on('mousemove', mousemove)
         .on('mouseleave', mouseleave);

      // Create sector titles
      svg.selectAll('.sector-title')
         .data(root.children)
         .enter()
         .append('text')
         .attr('class', 'sector-title')
         .attr('x', d => d.x0 + 5)
         .attr('y', d => d.y0 + 20)
         .text(d => d.data.name)
         .style('font-weight', 'bold')
         .style('font-size', '14px')
         .style('fill', 'black');



      // Here is the listener that updates the year
      
      document.getElementById('slider')
              .addEventListener('change', function() {
                  CurrentYear = this.value;
                  let new_data = allData.find(item => item.year == CurrentYear);
                  console.log("Current year is  ", CurrentYear);
                  console.log('all data is:', allData);
                  console.log("data is ", new_data);
                  updateVisualization(CurrentYear);
      });
      
      // This is the function update our data, 
      // I just copied the vis codes above, but the plot won't change with
      // CurrentYear.
      const updateVisualization = (year)=>{
          
          data = allData.find(item => item.year == year);

          // Extract unique sectors and assign colors
          const sectors = data.children.map(d => d.name);
          const colorScale = d3.scaleOrdinal(d3.schemeCategory10)
                           .domain(sectors);
          console.log(d3.select('#treemap'));
          // Create treemap using D3
          const width = 1400;
          const height = 1000;

          const svg = d3.select('#treemap')
                    .attr('width', width)
                    .attr('height', height);

          const treemap = d3.treemap()
                        .size([width, height])
                        .padding(1)
                        .round(true);

          const root = d3.hierarchy(data)
                     .sum(d => d.value)
                     .sort((a, b) => b.value - a.value); // Sort sectors by emission value

          treemap(root);

          const Tooltip = d3.select('body')
                          .append('div')
                          .attr('class', 'tooltip')
                          .style('opacity', 0)
                          .attr("class", "tooltip")
                          .style("background-color", "white")
                          .style("border", "solid")
                          .style("border-width", "2px")
                          .style("border-radius", "5px")
                          .style("padding", "5px")
                          .style("position", "absolute");

          const mouseover = function(event, d) {
              Tooltip.style('opacity', 1);
              d3.select(this)
              .style('stroke', 'black')
              .style('opacity', 1);
          };

          const mousemove = function(event, d) {
              Tooltip.html(`Country: ${d.data.name}<br>Greenhouse Emission: ${d.value/1000000} Million Tons `)
                      .style('left', (event.x) + 20 + "px")
                      .style('top', (event.y) + "px")
          };

          const mouseleave = function(event, d) {
              Tooltip.style('opacity', 0);
              d3.select(this)
              .style('stroke', 'none')
              .style('opacity', 0.8);
          };

          svg.selectAll('rect')
              .data(root.leaves())
              .enter()
              .append('rect')
              .attr('x', d => d.x0)
              .attr('y', d => d.y0)
              .attr('width', d => d.x1 - d.x0)
              .attr('height', d => d.y1 - d.y0)
              .attr('fill', d => colorScale(d.parent.data.name))
              .style('stroke-width', 4)
              .style('stroke', 'none')
              .style('opacity', 0.8)
              .on('mouseover', mouseover)
              .on('mousemove', mousemove)
              .on('mouseleave', mouseleave);

      // Create sector titles
          svg.selectAll('.sector-title')
              .data(root.children)
              .enter()
              .append('text')
              .attr('class', 'sector-title')
              .attr('x', d => d.x0 + 5)
              .attr('y', d => d.y0 + 20)
              .text(d => d.data.name)
              .style('font-weight', 'bold')
              .style('font-size', '14px')
              .style('fill', 'black');






      }


  });



</script>

<svg id="treemap"></svg>


<input id="slider" type="range" min="1990" max="2020" step="1" value="1990"/>

<style>
  .tooltip {
      position: absolute;
      background-color: white;
      border: solid;
      border-width: 2px;
      border-radius: 5px;
      padding: 5px;
  }

</style>