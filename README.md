# belly-button-challenge
<h2>Interactive Visualizations with Javascript</h2>

<h3>Overview</h3>
<div>
In this assignment, I utilized JavaScript and Plotly Library along with HTML and Bootstrap to create an interactive website dashboard. <a href="https://robdunnlab.com/projects/belly-button-biodiversity/">Belly Button Biodiversity dataset</a> was used for this project and reveals that a small handful of microbial species (also called operational taxonomic units, or OTUs, in the study) were present in more than 70% of people, while the rest were relatively rare.

<b>Reference:</b><br/>
Hulcr, J. et al. (2012) A Jungle in There: Bacteria in Belly Buttons are Highly Diverse, but Predictable. Retrieved from: http://robdunnlab.com/projects/belly-button-biodiversity/results-and-data/Links to an external site.

</div>
<hr/>
<div>
Goal:
<ul>
<li>Create a horizontal bar chart with a dropdown menu to display the top 10 OTUs found in that individual.</li>
<li>Create a bubble chart that displays each sample.</li>
<li>Display the sample's metadata, i.e., an individual's demographic information.</li>
<li>Update all the plots when a new sample is selected.</li>
<li>Deploy your app to a free static page hosting service, such as GitHub Pages</li>
</ul>

<h3>Tools and Techniques</h3>
<hr/>

<ul>
<li>Javascript</li>
<li>Plotly Library</li>
<li>HTML</li>
<li>Json</li>
<li>Bootstrap</li>
<li>d3</li>
</ul>

</div>

<h3>Project Structure</h3>
<hr/>


<h4>Part 1: build the metadata panel</h4>

```javascript
function buildMetadata(sample) {
  d3.json("https://static.bc-edx.com/data/dl-1-2/m14/lms/starter/samples.json").then((data) => {

    // Get the metadata field
    var metadata = data.metadata;

    // Filter the metadata for the object with the desired sample number
    var filteredArray = metadata.filter(sampleObject => sampleObject.id == sample);
    var result = filteredArray[0];

    // Use d3 to select the panel with id of `#sample-metadata`
    var panel = d3.select("#sample-metadata");

    // Use `.html("")` to clear any existing metadata
    panel.html("");

    // Inside a loop, use d3 to append new tags for each key-value in the filtered metadata.
    Object.entries(result).forEach(([key, value]) => {
      panel.append("h6").text(`${key.toUpperCase()}: ${value}`); })
    ;})
;}
```

<h4>Part 2: Build Bubble and Bar Chart</h4>

```javascript

// Function to build both charts
function buildCharts(sample) {
  d3.json("https://static.bc-edx.com/data/dl-1-2/m14/lms/starter/samples.json").then((data) => {

    // Get the samples field
    var samples = data.samples;

    // Filter the samples for the object with the desired sample number
    var selectedSample = samples.filter(sampleObject => sampleObject.id == sample);
    var result = selectedSample[0];

    // Get the otu_ids, otu_labels, and sample_values
    var otuIds = result.otu_ids;
    var otuLabels = result.otu_labels;
    var sampleValues = result.sample_values;

    // Build a Bubble Chart
    var bubbleLayout = {
      title: "Bacteria Cultures Per Sample",
      hovermode: "closest",
      xaxis: { title: "OTU ID" }
    };
    var bubbleData = [
      {
        x: otuIds,
        y: sampleValues,
        text: otuLabels,
        mode: "markers",
        marker: {
          size: sampleValues,
          color: otuIds,
          colorscale: "Earth"
        }
      }
    ];
    // Render the Bubble Chart
    Plotly.newPlot("bubble", bubbleData, bubbleLayout);

    // For the Bar Chart, map the otu_ids to a list of strings for your yticks
    var yticks = otuIds.slice(0, 10).map(otuID => `OTU ${otuID}`).reverse();

    // Build a Bar Chart
    var barData = [
      {
        y: yticks,
        x: sampleValues.slice(0, 10).reverse(),
        text: otuLabels.slice(0, 10).reverse(),
        type: "bar",
        orientation: "h",
      }
    ];

    var barLayout = {
      title: "Top 10 Bacteria Cultures Found",
      margin: { t: 30, l: 150 }
    };
    // Render the Bar Chart
    Plotly.newPlot("bar", barData, barLayout);
  });
}
```

<h4>Part 3: Deploy the app to a website</h4>


```javascript
// Function to run on page load
function init() {
  d3.json("https://static.bc-edx.com/data/dl-1-2/m14/lms/starter/samples.json").then((data) => {

    // Get the names field
    var names = data.names;

    // Use d3 to select the dropdown with id of `#selDataset`
    var selector = d3.select("#selDataset");

    // Use the list of sample names to populate the select options
    // Hint: Inside a loop, you will need to use d3 to append a new
    // option for each sample name.
    names.forEach((sample) => {
      selector
        .append("option")
        .text(sample)
        .property("value", sample);
    });

    // Get the first sample from the list
    var firstSample = names[0];

    // Build charts and metadata panel with the first sample
    buildCharts(firstSample);
    buildMetadata(firstSample);

  });
}

// Function for event listener
function optionChanged(newSample) {
  // Build charts and metadata panel each time a new sample is selected
  buildCharts(newSample);
  buildMetadata(newSample);
}

// Initialize the dashboard
init();
```


<h3>Conclusion</h3>
<hr/>
<p>
This assignment showcased how the interactive dashboard can be built with javascript and plotly libarary. the deployed website can be found in this <b><a href="https://sunjy0827.github.io/belly-button-challenge/">link</a></b>
</p>