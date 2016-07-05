---
layout: page
permalink: /has-he-failed/
---

<html>

<head>
    <center><h1 class="post-title">WOW</h1></center>
<!-- <center><h1 class="post-title">Has Matheus Portela failed #100DaysOfCode yet?</h1></center> -->
<script
src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.4/jquery.min.js">
</script>

<script>
function sleep(milliseconds)
{
  var start = new Date().getTime();
  for (var i = 0; i < 1e7; i++)
  {
    if ((new Date().getTime() - start) > milliseconds)
      break;
  }
}

$(document).ready(function()
{
    var starting_date = '';
    var iterator = 0;

    $.getJSON("https://api.github.com/repos/matheusportela/enigma-machine", function(data, status)
        {
            console.log("acquiring starting date...");
        })
        .done(function(data, status)
        {
            console.log("starting date acquired successfully!");

            starting_date = data.created_at;
            document.getElementById("div1").innerHTML += "starting date: " + starting_date + "<br><br>";

            var starting_date_string = '{"until": "' + "2016-06-24T22:59:41Z" + '"}'
            var starting_date_obj = JSON.parse(starting_date_string);
            $.getJSON("https://api.github.com/repos/matheusportela/enigma-machine/commits", starting_date_obj,
                function(data, status)
                {
                    console.log("acquiring commits...")
                })
                .done(function(data,status)
                {
                    console.log("commits acquired successfully!")

                    sleep(1000);
                    var commit_dates = [];
                    do
                    {
                        commit_dates.push(data[iterator].commit.author.date);
                        iterator++;
                    } while (data[iterator] != undefined)

                    document.getElementById("div1").innerHTML += commit_dates.pop();
                })
                .fail(function()
                {
                    console.log("error acquiring commits")
                })
                .always(function()
                {
                    console.log("acquiring commits complete")
                });
        })
        .fail(function()
        {
            console.log("error acquiring starting date");
        })
        .always(function()
        {
            console.log("acquiring starting date complete");
        });
});
</script>
</head>

<body>

<div id="div1"></div>

</body>
</html>