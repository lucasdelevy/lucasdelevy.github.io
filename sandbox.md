---
layout: page
permalink: /sandbox
---

<html>

<head>
<center><h1 class="post-title">SANDBOX</h1></center>
<script
src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.4/jquery.min.js">
</script>

<script>
$(document).ready(function()
{
        $.getJSON("https://api.github.com/repos/lucasdelevy/RoboRoach/commits", function(data, status)
            {
                var i = 0;
                do
                {
                    var commit = (i+1) + ") " + data[i].commit.author.date + "<br>";
                    document.getElementById("div1").innerHTML += commit;
                    i++;
                } while (commit)
            });
});
</script>
</head>

<body>

<div id="div1"></div>

</body>
</html>