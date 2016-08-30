---
layout: page
permalink: /game-of-life/
---

<html>

<head>
<style>
.grid { margin:1em auto; border-collapse:collapse }

.grid td
{
    /*cursor:pointer;*/
    width:20px; height:20px;
    border:2px solid #ccc;
}

.grid td.clicked
{
    background-color: gray;
}

.buttons_class
{
    text-align: center;
}

</style>

<center>
    <h1 class="post-title">GAME OF LIFE</h1>
</center>

<script>
// Grid functions
var nrows = 10;
var ncols = 30;
var grid = clickableGrid(nrows,ncols,
    function(el,row,col,i)
    {
        if (el.className == 'clicked')
            el.className = 'unclicked';
        else
            el.className = 'clicked'
    });

function clickableGrid(rows, cols, callback)
{
    var i = 0;
    var grid = document.createElement('table');
    grid.className = 'grid';
    for (var r = 0; r < rows; ++r){
        var tr = grid.appendChild(document.createElement('tr'));
        for (var c = 0; c < cols; ++c)
        {
            var cell = tr.appendChild(document.createElement('td'));
            cell.className = 'unclicked'

            cell.addEventListener('click',(
                function(el,r,c,i)
                {
                    return function()
                    {
                        callback(el,r,c,i);
                    }
                })(cell,r,c,i),false);
        }
    }
    return grid;
}

function getNeighborsStatistics(index_i, index_j)
{
    var stats = [0,0];

    for(var i = -1; i < 2; i++)
    {
        for(var j = -1; j < 2; j++)
        {
            if (i == 0 && j == 0)
                continue;

            these_rows = old_grid.rows[index_i+i]

            if(these_rows == undefined)
                continue;
            
            cell = these_rows.cells[index_j+j];

            if(cell == undefined)
                continue;

            if(cell.className == 'unclicked')
                stats[0]++;
            else if(cell.className == 'clicked')
                stats[1]++;
        }
    }

    return stats
}

// Game functions
var run_game = true;

function randomState()
{
    console.log('RANDOM!')

    for(var i = 0; i < nrows; i++)
    {
        for (var j = 0; j < ncols; j++)
        {
            random_cell = grid.rows[i].cells[j];

            if(Math.round(Math.random()) == 0)
                random_cell.className = 'unclicked';
            else
                random_cell.className = 'clicked';
        }
    }
}

function clearState()
{
    console.log('CLEAR!')

    for(var i = 0; i < nrows; i++)
    {
        for (var j = 0; j < ncols; j++)
        {
            random_cell = grid.rows[i].cells[j];
            random_cell.className = 'unclicked';
        }
    }
}

function playSimulation()
{
    old_grid = grid;

    for(var i = 0; i < nrows; i++)
    {
        for (var j = 0; j < ncols; j++)
        {
            game_cell = grid.rows[i].cells[j];

            // Check how many neighbors are dead (stats[0]) or alive (stats[1])
            stats = getNeighborsStatistics(i,j);
            
            // GAME OF LIFE
            // From: https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life
            // Obs.: Unclicked aka dead, clicked aka alive
            // 1. Any live cell with fewer than two live neighbours dies, as if caused by under-population.
            if(game_cell.className == 'clicked')
            {
                if(stats[1] < 2)
                    game_cell.className = 'unclicked';
            // 2. Any live cell with more than three live neighbours dies, as if by over-population.
                if(stats[1] > 3)
                    game_cell.className = 'unclicked'
            // 3. Any live cell with two or three live neighbours lives on to the next generation.
            }
            // 4. Any dead cell with exactly three live neighbours becomes a live cell, as if by reproduction.
            else if(stats[1] == 3)
                game_cell.className = 'clicked';
        }
    }
}

var start_button    = document.createElement('button')
var stop_button     = document.createElement('button')

var start_text  = document.createTextNode('START')
var stop_text   = document.createTextNode('STOP')

start_button.appendChild(start_text)
stop_button.appendChild(stop_text)

window.onload = function()
{
    // Game division
    var div_game = document.getElementById('div_game')
    div_game.appendChild(grid);

    // Button division
    var div_buttons = document.getElementById('div_buttons')
    var interval = null;

    start_button.disabled = false;
    stop_button.disabled = true;

    start_button.onclick = function()
    {
        var i = 0;
        start_button.disabled = true;
        stop_button.disabled = false;
        interval = setInterval(playSimulation, 100)
    };

    stop_button.onclick = function()
    {
        start_button.disabled = false;
        stop_button.disabled = true;
        clearInterval(interval);
    }

    div_buttons.appendChild(start_button);
    div_buttons.appendChild(stop_button);
}

</script>
</head>

<body>

<div id="div_game" class="game_class"> </div>

<div id="div_buttons" class="buttons_class">
    <button onclick="randomState()">RANDOM</button>
    <button onclick="clearState()">CLEAR</button>
</div>

Inspired on: http://stackoverflow.com/questions/9140101/creating-a-clickable-grid-in-a-web-browser

</body>

</html>