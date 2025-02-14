---
id: Running Shoe Stats
aliases:
  - Average Paces
tags: []
---
# Average Paces
```js
const pages = dv.pages('#Strava').where(p => p.gear_name && (p.sport_type === 'Run' ||p.sport_type === 'Walk' )).sort(p => p.start_date, 'asc');

const shoe_data = {};

pages.forEach(page => {
  const shoe = page.gear_name;
  const mileage = page.distance_miles;
  const total_time = page.elapsed_time / 60; // in minutes

  if (!shoe_data.hasOwnProperty(shoe)) {
    shoe_data[shoe] = {
      mileage: 0,
      total_time: 0,
    };
  }
  shoe_data[shoe].mileage += mileage;
  shoe_data[shoe].total_time += total_time;
});

const shoeArray = Object.entries(shoe_data).map(([shoe, data]) =>
						{const avg_pace = data.total_time / data.mileage;
						const pace = Duration.fromObject({ hours: avg_pace });
						 const avg_pace_fmt = pace.toFormat('hh:mm');
						return [shoe, data.mileage, avg_pace_fmt, avg_pace];});

dv.table(["Shoe", "Total Miles", "Average Pace"],
       shoeArray.sort((a, b) => a[3] - b[3]).map(([shoe, mileage, avg_pace_fmt]) => [shoe, Math.round(mileage*100)/100, avg_pace_fmt]))

```

