---
layout: page
permalink: /has-matheus-portela-failed/
---

<html>

<head>
    <!-- <center><h1 class="post-title">SANDBOX</h1></center> -->
    <center><h1 class="post-title">Has Matheus Portela failed #100DaysOfCode yet?</h1></center>
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

    var ajax_data_string = '{"since": "' + "2010-06-21T00:00:00Z" + '", ' +
                            '"per_page": 200}';
    var ajax_data_obj = JSON.parse(ajax_data_string);
    var commit_dates_str = [];
    var commit_dates = [];

    // Acquiring dates from commits
    $.getJSON("https://api.github.com/repos/matheusportela/enigma-machine/commits", ajax_data_obj)
        .done(function(data)
        {
            console.log("commits acquired successfully!")

            do
            {
                var date_to_add_str = data[iterator].commit.author.date.substring(0,10);
                if ($.inArray(date_to_add_str, commit_dates_str) == -1)
                {
                    var date_to_add = new Date(date_to_add_str.substring(0,4),
                                                date_to_add_str.substring(5,7)-1,
                                                date_to_add_str.substring(8,10));
                    commit_dates_str.push(date_to_add_str);
                    commit_dates.push(date_to_add);
                }

                iterator++;
            } while (data[iterator] != undefined)

            // Finding intervals
            var fail_flag = 0;
            for (iterator = 1; iterator < commit_dates.length; iterator++)
            {
                var day_diff = new Date(commit_dates[iterator-1] - commit_dates[iterator]);

                console.log(day_diff.getDate());

                if (day_diff.getDate() > 3)
                    fail_flag = 1;
            }

            if (fail_flag)
            {
                document.getElementById("div_result").innerHTML = "YES";
                document.getElementById("img_result").src = "/images/tcholas_fail.png";
                document.getElementById("img_result").width = "300";
            }
            else
            {
                document.getElementById("div_result").innerHTML = "NOT YET";
                document.getElementById("img_result").src = "/images/tcholas_success.png";
                document.getElementById("img_result").width = "400";
            }

            // Debugging code
            console.log("listing...");

            while (commit_dates.length > 0)
                console.log(commit_dates.length + ") " + commit_dates.pop());

            console.log("done listing!");
        });
});
</script>
</head>

<body>

<br>
<center>

    <h1><div id="div_result"></div></h1>
    <img src = "/images/loading.gif" id = "img_result" width = "100">

</center>

</body>
</html>