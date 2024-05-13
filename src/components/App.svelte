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
      
      // changed this so that all it does is update the rect sizes and titles
    const updateVisualization = (year) => {
        data = allData.find(item => item.year == year);

        const root = d3.hierarchy(data)
            .sum(d => d.value)
            .sort((a, b) => b.value - a.value); // Sort sectors by emission value

        treemap(root);

        svg.selectAll('rect')
            .data(root.leaves())
            .transition()
            .duration(500)
            .attr('x', d => d.x0)
            .attr('y', d => d.y0)
            .attr('width', d => d.x1 - d.x0)
            .attr('height', d => d.y1 - d.y0);

        svg.selectAll('.sector-title')
            .data(root.children)
            .text(d => d.data.name)
            .attr('x', d => d.x0 + 5)
            .attr('y', d => d.y0 + 20);
    }


  });



</script>

<div style="margin-bottom: 20px;">
  <input id="slider" type="range" min="1990" max="2020" step="1" value="1990" list="markers" style="width: 1400px;"/>

  <datalist id="markers">
    <option value="1990" label="1990"></option>
    <option value="1991" label="1991"></option>
    <option value="1992" label="1992"></option>
    <option value="1993" label="1993"></option>
    <option value="1994" label="1994"></option>
    <option value="1995" label="1995"></option>
    <option value="1996" label="1996"></option>
    <option value="1997" label="1997"></option>
    <option value="1998" label="1998"></option>
    <option value="1999" label="1999"></option>
    <option value="2000" label="2000"></option>
    <option value="2001" label="2001"></option>
    <option value="2002" label="2002"></option>
    <option value="2003" label="2003"></option>
    <option value="2004" label="2004"></option>
    <option value="2005" label="2005"></option>
    <option value="2006" label="2006"></option>
    <option value="2007" label="2007"></option>
    <option value="2008" label="2008"></option>
    <option value="2009" label="2009"></option>
    <option value="2010" label="2010"></option>
    <option value="2011" label="2011"></option>
    <option value="2012" label="2012"></option>
    <option value="2013" label="2013"></option>
    <option value="2014" label="2014"></option>
    <option value="2015" label="2015"></option>
    <option value="2016" label="2016"></option>
    <option value="2017" label="2017"></option>
    <option value="2018" label="2018"></option>
    <option value="2019" label="2019"></option>
    <option value="2020" label="2020"></option>
  </datalist>

</div>

<svg id="treemap"></svg>

<style>
  .tooltip {
      position: absolute;
      background-color: white;
      border: solid;
      border-width: 2px;
      border-radius: 5px;
      padding: 5px;
  }

  datalist {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    writing-mode: vertical-lr;
    width: 1400px;
  }

  option {
    padding: 0;
  }

  input[type="range"] {
    width: 200px;
    margin: 0;
  }

</style>