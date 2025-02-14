
# Training Summary

```js
const race_date = "<% tp.frontmatter["start_date"] %>"

const start_date = new Date(race_date);

var end_date = new Date(race_date);
end_date.setDate(end_date.getDate() - 63);

if(moment(end_date).weekday() != 0){
	end_date.setDate(end_date.getDate() - moment(end_date).weekday());
}

const pages = dv.pages('#Strava').where(p => p.start_date && (p.sport_type === 'Run' ||p.sport_type === 'Walk' )&& p.start_date.ts <= start_date && p.start_date >= end_date).sort(p => p.start_date, 'asc');
let dates = pages.map(p => p.start_date.ts).values
const weekData = {};
const longRunData = {};

const race_day_miles_list = pages.where(p => p.start_date.ts == moment(start_date).valueOf()).values;
let race_day_miles =0; 
race_day_miles_list.forEach(p => {race_day_miles +=p.distance;});

pages.forEach(page => {
  const week = moment(page.start_date.ts).format('gggg-WW');
  const runningDistance = page.sport_type === 'Run' ? page.distance : 0;

  if (!weekData.hasOwnProperty(week)) {
    weekData[week] = {
      runningDistance: 0,
    };
  }
  if (!longRunData.hasOwnProperty(week)) {
    longRunData[week] = {
      runningDistance: 0,
    };
  }
  if (longRunData[week].runningDistance < runningDistance){
	  longRunData[week].runningDistance = runningDistance;
  }

  weekData[week].runningDistance += runningDistance ;


});

const weeks = Object.keys(weekData);
const runningDistance = Object.values(weekData).map(data => data.runningDistance * 0.000621371);

const longRunDistance = Object.values(longRunData).map(data => data.runningDistance * 0.000621371);

const chartData = {
  type: 'bar',
  data: {
    labels: weeks,
    datasets: [
      {
        label: '🏃 Weekly Miles',
        data: runningDistance,
        backgroundColor: [ 'rgba(54, 162, 235, 0.2)' ],
    borderColor: [ 'rgba(54, 162, 235, 1)' ],
        borderWidth: 1
      },
      {
        label: '🏃 Long Run Distance',
        data: longRunDistance,
        backgroundColor: [ 'rgba(120, 81, 169, 0.2)' ],
    borderColor: [ 'rgba(120, 81, 169, 1)' ],
        borderWidth: 1
      }
    ]
  }
}
window.renderChart(chartData, this.container)

let total = runningDistance.reduce((acc, mile) => {return acc += mile})
let avg = total / weeks.length;

dv.paragraph("Average weekly miles: " + avg.toString())


///////////////// Listed activities

dates = pages.map(p => p.start_date).sort(p => p.start_date, 'desc').values
const miles = pages.sort(p => p.start_date, 'desc').map(p => p.distance  *0.000621371).values

total = (Math.round((total-race_day_miles * 0.000621371) *100) / 100)
dv.paragraph((total).toString() + " miles in the ~2 months leading up the race(" + start_date.toLocaleDateString('en-US') + " - " + end_date.toLocaleDateString('en-US') + ")" );


dv.table(["Date", "Activity", "Distance", "Avg Pace"],
		//pages.where(p => p.sport_type === 'Run')
		pages
		.sort(p => p.start_date, 'desc')
		.map(p => 
		{
		const avg_pace = p.elapsed_time / (60* (Math.round(p.distance *0.0621371) / 100));
						const pace = Duration.fromObject({ hours: avg_pace });
						 const avg_pace_fmt = pace.toFormat('hh:mm');
		return [p.start_date.toFormat("yyyy-MM-dd"), p.name, Math.round(p.distance *0.0621371) / 100, avg_pace_fmt]})
		)

```
