---
title: "Enrollment vs Endowment: Big Ten and SEC"
layout: single
excerpt_separator: "<!--more-->"
categories:
  - blog
tags:
  - colleges
  - universities
  - schools
  - conferences
  - enrollment
  - endowment
  - charts
  - data
date: 2024-12-11 10:00:00 EST
timezone: America/New_York
search: true
toc: false
---

<script src="https://cdn.plot.ly/plotly-2.20.0.min.js"></script>

With conference realignment in full swing, how do new **Big Ten** and **SEC** schools fit in with their peers in terms of total **enrollment** and **endowment**? Check out the chart below to see! *(note that the y axis is in logarithmic scale)*

<div id="chart" style="width:100%;height:600px;"></div>

## Sources
[Big Ten](https://en.wikipedia.org/wiki/Big_Ten_Conference)
[SEC](https://en.wikipedia.org/wiki/Southeastern_Conference)

<script>
  // Data for SEC and Big Ten schools
  const data = [
    {school: "UCLA", conference: "Big Ten", type: "Public", enrollment: 48.048, endowment: 3.873, color: "lightblue", shape: "circle"},
    {school: "Illinois", conference: "Big Ten", type: "Public", enrollment: 56.403, endowment: 3.38, color: "lightblue", shape: "circle"},
    {school: "Indiana", conference: "Big Ten", type: "Public", enrollment: 47.527, endowment: 3.56, color: "lightblue", shape: "circle"},
    {school: "Iowa", conference: "Big Ten", type: "Public", enrollment: 31.452, endowment: 3.258, color: "lightblue", shape: "circle"},
    {school: "Maryland", conference: "Big Ten", type: "Public", enrollment: 40.813, endowment: 2.1, color: "lightblue", shape: "circle"},
    {school: "Michigan", conference: "Big Ten", type: "Public", enrollment: 52.065, endowment: 17.876, color: "lightblue", shape: "circle"},
    {school: "Michigan State", conference: "Big Ten", type: "Public", enrollment: 51.316, endowment: 4.054, color: "lightblue", shape: "circle"},
    {school: "Minnesota", conference: "Big Ten", type: "Public", enrollment: 54.890, endowment: 5.501, color: "lightblue", shape: "circle"},
    {school: "Nebraska", conference: "Big Ten", type: "Public", enrollment: 23.600, endowment: 2.27, color: "lightblue", shape: "circle"},
    {school: "Northwestern", conference: "Big Ten", type: "Private", enrollment: 22.801, endowment: 13.7, color: "lightblue", shape: "diamond"},
    {school: "Ohio State", conference: "Big Ten", type: "Public", enrollment: 60.046, endowment: 7.384, color: "lightblue", shape: "circle"},
    {school: "Oregon", conference: "Big Ten", type: "Public", enrollment: 23.834, endowment: 1.49, color: "lightblue", shape: "circle"},
    {school: "Penn State", conference: "Big Ten", type: "Public", enrollment: 48.535, endowment: 4.444, color: "lightblue", shape: "circle"},
    {school: "Purdue", conference: "Big Ten", type: "Public", enrollment: 52.211, endowment: 3.79, color: "lightblue", shape: "circle"},
    {school: "Rutgers", conference: "Big Ten", type: "Public", enrollment: 50.617, endowment: 1.99, color: "lightblue", shape: "circle"},
    {school: "USC", conference: "Big Ten", type: "Private", enrollment: 47.147, endowment: 7.463, color: "lightblue", shape: "diamond"},
    {school: "Washington", conference: "Big Ten", type: "Public", enrollment: 60.692, endowment: 4.941, color: "lightblue", shape: "circle"},
    {school: "Wisconsin", conference: "Big Ten", type: "Public", enrollment: 50.662, endowment: 3.838, color: "lightblue", shape: "circle"},
    {school: "Alabama", conference: "SEC", type: "Public", enrollment: 39.623, endowment: 2.09, color: "yellow", shape: "circle"},
    {school: "Arkansas", conference: "SEC", type: "Public", enrollment: 32.140, endowment: 1.527, color: "yellow", shape: "circle"},
    {school: "Auburn", conference: "SEC", type: "Public", enrollment: 33.015, endowment: 1.079, color: "yellow", shape: "circle"},
    {school: "Florida", conference: "SEC", type: "Public", enrollment: 60.489, endowment: 2.337, color: "yellow", shape: "circle"},
    {school: "Georgia", conference: "SEC", type: "Public", enrollment: 40.118, endowment: 1.811, color: "yellow", shape: "circle"},
    {school: "Kentucky", conference: "SEC", type: "Public", enrollment: 33.885, endowment: 2.13, color: "yellow", shape: "circle"},
    {school: "LSU", conference: "SEC", type: "Public", enrollment: 39.419, endowment: 1.06, color: "yellow", shape: "circle"},
    {school: "Ole Miss", conference: "SEC", type: "Public", enrollment: 24.710, endowment: 0.836, color: "yellow", shape: "circle"},
    {school: "Mississippi State", conference: "SEC", type: "Public", enrollment: 22.657, endowment: 0.709, color: "yellow", shape: "circle"},
    {school: "Missouri", conference: "SEC", type: "Public", enrollment: 31.041, endowment: 2.24, color: "yellow", shape: "circle"},
    {school: "Oklahoma", conference: "SEC", type: "Public", enrollment: 32.676, endowment: 1.67, color: "yellow", shape: "circle"},
    {school: "South Carolina", conference: "SEC", type: "Public", enrollment: 36.538, endowment: 0.952, color: "yellow", shape: "circle"},
    {school: "Tennessee", conference: "SEC", type: "Public", enrollment: 36.304, endowment: 1.6, color: "yellow", shape: "circle"},
    {school: "Texas", conference: "SEC", type: "Public", enrollment: 53.082, endowment: 44.97, color: "yellow", shape: "circle"},
    {school: "Texas A&M", conference: "SEC", type: "Public", enrollment: 77.491, endowment: 19.29, color: "yellow", shape: "circle"},
    {school: "Vanderbilt", conference: "SEC", type: "Private", enrollment: 13.456, endowment: 9.684, color: "yellow", shape: "diamond"}
  ];

  const traces = data.map(d => ({
  x: [d.enrollment],
  y: [d.endowment],
  mode: "markers+text",
  marker: {
    size: 10,
    color: d.color,
    symbol: d.shape,
    line: {
      color: "black", // Black outline
      width: 1        // Thin outline
    }
  },
  name: d.school,  // Use this for legend label
  text: [d.school],  // Show just the school name on the point
  textfont: {
    color: d.color,
    size: 10
  },
  textposition: 'top center',  // Position for the label
  hoverinfo: 'text',  // Only show the text (hovertext)
  hovertext: `School: ${d.school}<br>Conference: ${d.conference}<br>Enrollment: ${d.enrollment}<br>Endowment: $${d.endowment}B<br>Type: ${d.type}` // Detailed info in the tooltip
}));

// Layout with dark theme customization
const layout = {
  title: {
    text: "Endowment vs. Enrollment",
    font: { color: "#ffffff" }
  },
  xaxis: {
    title: { text: "Enrollment (Thousands)", font: { color: "#ffffff" } },
    tickfont: { color: "#ffffff" },
    gridcolor: "#666666"
  },
  yaxis: {
    title: { text: "Endowment (Billions)", font: { color: "#ffffff" } },
    tickfont: { color: "#ffffff" },
    type: 'log',
    gridcolor: "#666666"
  },
  plot_bgcolor: "#333333",
  paper_bgcolor: "#333333",
  showlegend: false,
  margin: {
    l: 30,
    r: 15,
    t: 30,
    b: 30
  }
};

// Render the chart
Plotly.newPlot("chart", traces, layout);
</script>
