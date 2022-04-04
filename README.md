# JavaScript Challenge - Javascript and DOM Manipulation
WAKE UP SHEEPLE! The extra-terrestrial menace has come to Earth and we here at ALIENS-R-REAL have collected all of the eye-witness reports we could to prove it! All we need to do now is put this information online for the world to see and then the matter will finally be put to rest.
There is just one tiny problem though... our collection is too large to search through manually. Even our most dedicated followers are complaining that they are having trouble locating specific reports in this mess.

That's why we are hiring you. We need you to write code that will create a table dynamically based upon a [dataset we provide](https://nu.bootcampcontent.com/NU-Coding-Bootcamp/nu-chi-data-pt-06-2021-u-c/-/blob/master/14-Intro-To-JavaScript/Homework/due_Oct4/Instructions/StarterCode/static/js/data.js). We also need to allow our users to filter the table data for specific values. There's a catch though... we only use pure JavaScript, HTML, and CSS, and D3.js on our web pages. They are the only coding languages which can be trusted.
You can handle this... right? The planet Earth needs to know what we have found!

-----

## Level 1: Automatic Table and Date Search
* Create a basic HTML web page

```html
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <title>UFO Finder</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootswatch/3.3.7/superhero/bootstrap.min.css">
  <link href="https://fonts.googleapis.com/css?family=Ubuntu" rel="stylesheet">
  <link rel="stylesheet" href="static/css/style.css">
</head>

<body>
  <div class="wrapper">
    <nav class="navbar navbar-default">
      <div class="container-fluid">
        <div class="navbar-header">
          <a class="navbar-brand" href="index.html">UFO Sightings
            <img class="nav-ufo" src="static/images/ufo.svg">
          </a>
        </div>
      </div>
    </nav>
    <div class="hero text-center">
      <h1>UFO Sightings</h1>
      <p>The Truth is Out There</p>
    </div>
    <div class="container">
      <div class="row margin-top-50">
        <div class="col-md-2">
          <aside class="filters">
            <div class="panel panel-default">
              <div class="panel-heading">Filter Search</div>
              <div class="panel-body">
                <form>
                  <div class="form-group">
                    <ul class="list-group" id="filters">
                      <li class="filter list-group-item">
                        <label for="date">Enter a Date</label>
                        <input class="form-control" id="datetime" type="text" placeholder="1/11/2011">
                      </li>
                    </ul>
                  </div>
                  <button id="filter-btn" type="button" class="btn btn-default">Filter Table</button>
                </form>
              </div>
            </div>
          </aside>
        </div>
        <div class="col-md-10">
          <div id="table-area" class="">
            <table id="ufo-table" class="table table-striped">
              <thead>
                <tr>
                  <th class="table-head">Date</th>
                  <th class="table-head">City</th>
                  <th class="table-head">State</th>
                  <th class="table-head">Country</th>
                  <th class="table-head">Shape</th>
                  <th class="table-head">Duration</th>
                  <th class="table-head">Comments</th>
                </tr>
              </thead>
              <tbody></tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
    <footer class="footer">
      <span class="bottom">UFO Sightings</span>
    </footer>
  </div>
  <script src="https://d3js.org/d3.v5.min.js"></script>
  <script src="static/js/data.js"></script>
  <script src="static/js/app.js"></script>
</body>

</html>
```

* Using the UFO dataset provided in the form of an array of JavaScript objects, write code that appends a table to your web page and then adds new rows of data for each UFO sighting. Include columns for: date/time, city, state, country, shape, and comment.

```js
// from data.js
const tableData = data;

// get table references
const tbody = d3.select("tbody");

function buildTable(data) {
  // First, clear out any existing data
  tbody.html("");

  // Next, loop through each object in the data
  // and append a row and cells for each value in the row
  data.forEach((dataRow) => {
    // Append a row to the table body
    const row = tbody.append("tr");

    // Loop through each field in the dataRow and add
    // each value as a table cell (td)
    Object.values(dataRow).forEach((val) => {
      let cell = row.append("td");
        cell.text(val);
      }
    );
  });
}

function handleClick() {

  // Grab the datetime value from the filter
  const date = d3.select("#datetime").property("value");
  let filteredData = tableData;

  // Check to see if a date was entered and filter the
  // data using that date.
  if (date) {
    // Apply `filter` to the table data to only keep the
    // rows where the `datetime` value matches the filter value
    filteredData = filteredData.filter(row => row.datetime === date);
  }

  // Rebuild the table using the filtered data
  // @NOTE: If no date was entered, then filteredData will
  // just be the original tableData.
  buildTable(filteredData);
}

// Attach an event to listen for the form button
d3.selectAll("#filter-btn").on("click", handleClick);

// Build the table when the page loads
buildTable(tableData);
```

-----

## Level 2: Multiple Search Categories
* Using multiple input tags and/or select dropdowns, write JavaScript code so the user can set multiple filters and search for UFO sightings based on the table columns: date/time, city, state, country, and shape.

```js
// from data.js
var tableData = data;

// get table references
var tbody = d3.select("tbody");

function buildTable(data) {
  // First, clear out any existing data
  tbody.html("");

  // Next, loop through each object in the data
  // and append a row and cells for each value in the row
  data.forEach((dataRow) => {
    // Append a row to the table body
    var row = tbody.append("tr");

    // Loop through each field in the dataRow and add
    // each value as a table cell (td)
    Object.values(dataRow).forEach((val) => {
      var cell = row.append("td");
      cell.text(val);
    });
  });
}

// Keep Track of all filters
var filters = {};

function updateFilters() {

  // Save the element, value, and id of the filter that was changed
  var changedElement = d3.select(this).select("input");
  var elementValue = changedElement.property("value");
  var filterId = changedElement.attr("id");

  // If a filter value was entered then add that filterId and value
  // to the filters list. Otherwise, clear that filter from the filters object
  if (elementValue) {
    filters[filterId] = elementValue;
  }
  else {
    delete filters[filterId];
  }

  // Call function to apply all filters and rebuild the table
  filterTable();

}

function filterTable() {

  // Set the filteredData to the tableData
  let filteredData = tableData;

  // Loop through all of the filters and keep any data that
  // matches the filter values
  Object.entries(filters).forEach(([key, value]) => {
    filteredData = filteredData.filter(row => row[key] === value);
  });

  // Finally, rebuild the table using the filtered Data
  buildTable(filteredData);
}

// Attach an event to listen for changes to each filter
d3.selectAll(".filter").on("change", updateFilters);

// Build the table when the page loads
buildTable(tableData);
```