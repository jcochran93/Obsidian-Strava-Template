---
cssclasses:
  - dashboard
---

```js
const pages = dv.pages('#Strava')
const dates = pages.map(p => p.start_date).values
const yearData = {};

pages.forEach(page => {
  const year = moment(page.start_date.ts).format('YYYY');
  const ridingDistance = page.sport_type === 'Ride' ? page.distance : 0;
  const walkingDistance = page.sport_type === 'Walk' ? page.distance : 0;
  const runningDistance = page.sport_type === 'Run' ? page.distance : 0;

  if (!yearData.hasOwnProperty(year)) {
    yearData[year] = {
      ridingDistance: 0,
      runningDistance: 0,
      walkingDistance: 0
    };
  }

  yearData[year].ridingDistance += ridingDistance * 0.000621371;
  yearData[year].runningDistance += runningDistance * 0.000621371;
  yearData[year].walkingDistance += walkingDistance * 0.000621371;



});

const years = Object.keys(yearData);
const ridingDistance = Object.values(yearData).map(data => data.ridingDistance);
const runningDistance = Object.values(yearData).map(data => data.runningDistance);
const walkingDistance = Object.values(yearData).map(data => data.walkingDistance);

const chartData = {
  type: 'bar',
  data: {
    labels: years,
    datasets: [
      {
        label: '🚶🏻‍♂️ Walking distance',
        data: walkingDistance,
        backgroundColor: [ 'rgba(120, 81, 169, 0.2)' ],
    borderColor: [ 'rgba(120, 81, 169, 1)' ],
        borderWidth: 1
      },
      {
        label: '🚴 Riding distance',
        data: ridingDistance,
        backgroundColor: [ 'rgba(255, 99, 132, 0.2)' ],
        borderColor: [ 'rgba(255, 99, 132, 1)' ],
        borderWidth: 1
      },
      {
        label: '🏃 Running distance',
        data: runningDistance,
        backgroundColor: [ 'rgba(54, 162, 235, 0.2)' ],
    borderColor: [ 'rgba(54, 162, 235, 1)' ],
        borderWidth: 1
      }
    ]
  }
}
window.renderChart(chartData, this.container)
```

```contributionGraph
graphType: default
dateRangeValue: 365
dateRangeType: LATEST_DAYS
startOfWeek: "1"
showCellRuleIndicators: true
titleStyle:
  textAlign: left
  fontSize: 15px
  fontWeight: normal
dataSource:
  type: PAGE
  value: "#Strava"
  dateField:
    type: PAGE_PROPERTY
    value: start_date
  countField:
    type: PAGE_PROPERTY
    value: distance_miles
fillTheScreen: false
enableMainContainerShadow: false
cellStyleRules:
  - id: Ocean_a
    color: "#8dd1e2"
    min: 1
    max: 3
  - id: Ocean_b
    color: "#63a1be"
    min: 3
    max: 6
  - id: Ocean_c
    color: "#376d93"
    min: 6
    max: 9
  - id: Ocean_d
    color: "#012f60"
    min: 9
    max: 1000
cellStyle:
  minWidth: 10px
  minHeight: 10px

```

```js
const currentYear = new Date().getFullYear()
const from = currentYear + '-01-01'
const to = currentYear + '-12-31'

const pages = dv.pages('#Strava').where(p => p.start_date && p.sport_type === 'Run');
const dates = pages.map(p => p.start_date).values

const data = dates
	.map(entry =>{
		return {
			date: moment(entry.ts).format('YYYY-MM-DD'),
			value: Math.round((pages.where(p=> p.start_date == entry)[0].distance * 0.0621371)) / 100
		}
	})

const calendarData = {
    title:  `${from} to ${to}`,
    data: data,
    fromDate: from,
    toDate: to,
    graphType: "month-track", //calendar
    cellStyleRules: [
		{
			color: "#8dd1e2",
			min: 1,
			max: 4,
		},
		{
			color: "#63a1be",
			min: 4,
			max: 8,
		},
		{
			color: "#376d93",
			min: 8,
			max: 13,
		},
		{
			color: "#012f60",
			min: 13,
			max: 100000,
		},
	]
}

renderContributionGraph(this.container, calendarData)
```


```js
const pages = dv.pages('#Strava').where(p => p.start_date >= new Date('2025-01-01'));
const dates = pages.map(p => p.start_date).values
const yearData = [];

pages.forEach(page => {
  const day = moment(page.start_date.ts).format('YYYY-MM-DD');
  const runningDistance = page.sport_type === 'Run' ? page.distance : 0;

  //if (!yearData.hasOwnProperty(day)) {
    //yearData[day] = {
     // runningDistance: 0
    //};
  //}
 
  //yearData[day].runningDistance += runningDistance * 0.000621371;
if (!yearData.hasOwnProperty(day)) {
    yearData[day] = 0;}
yearData[day] += runningDistance * 0.000621371;


});

const sortedData = Object.entries(yearData)
.sort((a, b) => new Date(a[0]) - new Date(b[0])).reduce((obj, [key, value]) => {  obj[key] = value; return obj; }, {});

const days = Object.keys(sortedData);
const runningDistance = Object.values(sortedData).map(data => data.runningDistance);

const chartData = {
  type: 'line',
  data: {
    labels: days,
    datasets: [
      {
        label: '🏃 Running distance',
        data: Object.values(sortedData),
        backgroundColor: [ 'rgba(54, 162, 235, 0.2)' ],
    borderColor: [ 'rgba(54, 162, 235, 1)' ],
        borderWidth: 1
      }
    ]
  }
}
window.renderChart(chartData, this.container)
```



```js

const pages = dv.pages('#Strava')
const dates = pages.map(p => p.start_date).values
const yearData = {};

dv.table(["Date", "Activity", "Distance"],
		//pages.where(p => p.sport_type === 'Run')
		pages
		.sort(p => p.start_date, 'desc')
		.limit(30)
		.map(p => [p.start_date.toFormat("yyyy-MM-dd"), p.name, Math.round(p.distance *0.0621371) / 100])
		)


```
