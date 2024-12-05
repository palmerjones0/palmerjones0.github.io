---
title: "CFB National Championship Coach Trends"
layout: single
excerpt_separator: "<!--more-->"
categories:
  - blog
tags:
  - football
  - college football
  - statistics
  - sports science
  - charts
  - data
  - coaching
date: 2024-12-04 10:00:00 EST
timezone: America/New_York
search: true
toc: true
---

There's a healthy crop of college football coaches who are actively seeking out their first championship win. But, if any of them were to win it all this year, how would they compare to the winners over the last 50 years? Take a look at the chart below to see how these *aspiring coaches (red)* would stack up against *past winners (blue)* in terms of **age** and the **number or years at their current program**.

<canvas id="coachesChart" width="800" height="400"></canvas>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
var ctx = document.getElementById('coachesChart').getContext('2d');
var coachesData = {
  labels: [
    '2023', '2022', '2021', '2020', '2019', '2018', '2017', '2016', '2015', '2014', 
    '2013', '2012', '2011', '2010', '2009', '2008', '2007', '2006', '2005', '2004', 
    '2003', '2002', '2001', '2000', '1999', '1998', '1997', '1996', '1995', '1994', 
    '1993', '1992', '1991', '1990', '1989', '1988', '1987', '1986', '1985', '1984', 
    '1983', '1982', '1981', '1980', '1979', '1978', '1977', '1976', '1975', '1974'
  ],
  datasets: [{
    label: 'Coach Tenure vs. Age at Title',
    data: [
      { x: 60, y: 9, titleYear: 2023, coach: 'Jim Harbaugh', school: 'Michigan' },
      { x: 47, y: 7, titleYear: 2022, coach: 'Kirby Smart', school: 'Georgia' },
      { x: 46, y: 6, titleYear: 2021, coach: 'Kirby Smart', school: 'Georgia' },
      { x: 69, y: 14, titleYear: 2020, coach: 'Nick Saban', school: 'Alabama' },
      { x: 58, y: 3, titleYear: 2019, coach: 'Ed Orgeron', school: 'LSU' },
      { x: 49, y: 10, titleYear: 2018, coach: 'Dabo Swinney', school: 'Clemson' },
      { x: 66, y: 11, titleYear: 2017, coach: 'Nick Saban', school: 'Alabama' },
      { x: 48, y: 8, titleYear: 2016, coach: 'Dabo Swinney', school: 'Clemson' },
      { x: 64, y: 9, titleYear: 2015, coach: 'Nick Saban', school: 'Alabama' },
      { x: 50, y: 3, titleYear: 2014, coach: 'Urban Meyer', school: 'Ohio State' },
      { x: 48, y: 4, titleYear: 2013, coach: 'Jimbo Fisher', school: 'Florida State' },
      { x: 61, y: 6, titleYear: 2012, coach: 'Nick Saban', school: 'Alabama' },
      { x: 60, y: 5, titleYear: 2011, coach: 'Nick Saban', school: 'Alabama' },
      { x: 49, y: 2, titleYear: 2010, coach: 'Gene Chizik', school: 'Auburn' },
      { x: 58, y: 3, titleYear: 2009, coach: 'Nick Saban', school: 'Alabama' },
      { x: 44, y: 4, titleYear: 2008, coach: 'Urban Meyer', school: 'Florida' },
      { x: 54, y: 3, titleYear: 2007, coach: 'Les Miles', school: 'LSU' },
      { x: 42, y: 4, titleYear: 2006, coach: 'Urban Meyer', school: 'Florida' },
      { x: 54, y: 22, titleYear: 2005, coach: 'Mack Brown', school: 'Texas' },
      { x: 53, y: 6, titleYear: 2004, coach: 'Pete Carroll', school: 'USC' },
      { x: 52, y: 3, titleYear: 2003, coach: 'Nick Saban', school: 'LSU' },
      { x: 49, y: 17, titleYear: 2002, coach: 'Jim Tressel', school: 'Ohio State' },
      { x: 54, y: 1, titleYear: 2001, coach: 'Larry Coker', school: 'Miami (FL)' },
      { x: 40, y: 2, titleYear: 2000, coach: 'Bob Stoops', school: 'Oklahoma' },
      { x: 70, y: 34, titleYear: 1999, coach: 'Bobby Bowden', school: 'Florida State' },
      { x: 48, y: 6, titleYear: 1998, coach: 'Phillip Fulmer', school: 'Tennessee' },
      { x: 52, y: 3, titleYear: 1997, coach: 'Lloyd Carr', school: 'Michigan' },
      { x: 51, y: 7, titleYear: 1996, coach: 'Steve Spurrier', school: 'Florida' },
      { x: 58, y: 23, titleYear: 1995, coach: 'Tom Osborne', school: 'Nebraska' },
      { x: 57, y: 22, titleYear: 1994, coach: 'Tom Osborne', school: 'Nebraska' },
      { x: 64, y: 28, titleYear: 1993, coach: 'Bobby Bowden', school: 'Florida State' },
      { x: 57, y: 15, titleYear: 1992, coach: 'Gene Stallings', school: 'Alabama' },
      { x: 44, y: 13, titleYear: 1991, coach: 'Dennis Erickson', school: 'Miami (FL)' },
      { x: 50, y: 9, titleYear: 1990, coach: 'Bill McCartney', school: 'Colorado' },
      { x: 42, y: 11, titleYear: 1989, coach: 'Dennis Erickson', school: 'Miami (FL)' },
      { x: 51, y: 17, titleYear: 1988, coach: 'Lou Holtz', school: 'Notre Dame' },
      { x: 44, y: 9, titleYear: 1987, coach: 'Jimmy Johnson', school: 'Miami (FL)' },
      { x: 60, y: 21, titleYear: 1986, coach: 'Joe Paterno', school: 'Penn State' },
      { x: 48, y: 13, titleYear: 1985, coach: 'Barry Switzer', school: 'Oklahoma' },
      { x: 54, y: 13, titleYear: 1984, coach: 'LaVell Edwards', school: 'BYU' },
      { x: 49, y: 9, titleYear: 1983, coach: 'Howard Schnellenberger', school: 'Miami (FL)' },
      { x: 56, y: 17, titleYear: 1982, coach: 'Joe Paterno', school: 'Penn State' },
      { x: 33, y: 4, titleYear: 1981, coach: 'Danny Ford', school: 'Clemson' },
      { x: 48, y: 17, titleYear: 1980, coach: 'Vince Dooley', school: 'Georgia' },
      { x: 66, y: 31, titleYear: 1979, coach: 'Bear Bryant', school: 'Alabama' },
      { x: 65, y: 30, titleYear: 1978, coach: 'Bear Bryant', school: 'Alabama' },
      { x: 53, y: 18, titleYear: 1977, coach: 'Dan Devine', school: 'Notre Dame' },
      { x: 41, y: 9, titleYear: 1976, coach: 'Johnny Majors', school: 'Pittsburgh' },
      { x: 38, y: 3, titleYear: 1975, coach: 'Barry Switzer', school: 'Oklahoma' },
      { x: 37, y: 2, titleYear: 1974, coach: 'Barry Switzer', school: 'Oklahoma' },
      // 2024 coaches shown in red
      { x: 49, y: 5, titleYear: 2024, coach: 'Lane Kiffin', school: 'Ole Miss', backgroundColor: 'red' },
      { x: 45, y: 6, titleYear: 2024, coach: 'Ryan Day', school: 'Ohio State', backgroundColor: 'red' },
      { x: 62, y: 3, titleYear: 2024, coach: 'Brian Kelly', school: 'LSU', backgroundColor: 'red' },
      { x: 38, y: 3, titleYear: 2024, coach: 'Marcus Freeman', school: 'Notre Dame', backgroundColor: 'red' },
      { x: 45, y: 3, titleYear: 2024, coach: 'Josh Heupel', school: 'Tennessee', backgroundColor: 'red' },
      { x: 50, y: 4, titleYear: 2024, coach: 'Steve Sarkisian', school: 'Texas', backgroundColor: 'red' },
      { x: 41, y: 3, titleYear: 2024, coach: 'Lincoln Riley', school: 'USC', backgroundColor: 'red' }
    ],
    backgroundColor: function(context) {
      var dataIndex = context.dataIndex;
      return coachesData.datasets[0].data[dataIndex].backgroundColor || 'lightblue'; // Default color is blue
    },
    //backgroundColor: 'rgba(54, 162, 235, 0.2)',
    borderColor: 'rgba(54, 162, 235, 1)',
    borderWidth: 1
  }]
};

var coachesChart = new Chart(ctx, {
  type: 'scatter',
  data: coachesData,
  options: {
    plugins: {
      tooltip: {
        callbacks: {
          title: function(tooltipItem) {
            const data = tooltipItem[0].raw;
            return `Title Year: ${data.titleYear}`;
          },
          label: function(tooltipItem) {
            const data = tooltipItem.raw;
            return `${data.coach} (${data.school}) - Age: ${data.x}, Tenure: ${data.y} years`;
          }
        }
      }
    },
    scales: {
      x: {
        title: {
          display: true,
          text: 'Age at Title'
        }
      },
      y: {
        title: {
          display: true,
          text: 'Coaching Tenure (Years)'
        }
      }
    }
  }
});
</script>

If you see any issues with the data please reach out or leave a comment below!