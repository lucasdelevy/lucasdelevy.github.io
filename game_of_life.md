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
    width:4px; height:4px;
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

.credits_class
{
    font-size: 80%;
    text-align: center;
    font-style: italic;
}

</style>

<center>
    <h1 class="post-title"><b>GAME OF LIFE</b></h1>
</center>

<script>
// Grid functions
var nrows = 20;
var ncols = 80;
var grid = clickableGrid(nrows,ncols,
    function(el,row,col,i)
    {
        el.className = el.className == 'clicked' ? 'unclicked' : 'clicked'
    });

var mouse_down = false;
function clickableGrid(rows, cols, callback)
{
    var grid = document.createElement('table');
    grid.className = 'grid';

    var i = 0;
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
            cell.addEventListener('mousedown', (
                function(el,r,c,i)
                {
                    return function()
                    {
                        mouse_down = true;
                    }
                })(cell,r,c,i),false);
            cell.addEventListener('mouseup', (
                function(el,r,c,i)
                {
                    return function()
                    {
                        mouse_down = false;
                    }
                })(cell,r,c,i),false);
            cell.addEventListener('mouseenter',(
                function(el,r,c,i)
                {
                    return function()
                    {
                        if(mouse_down)
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

function startGame()
{
    var i = 0;
    start_button.disabled = true;
    stop_button.disabled = false;
    interval = setInterval(playSimulation, 100)
};

function stopGame()
{
    start_button.disabled = false;
    stop_button.disabled = true;
    clearInterval(interval);
}

// Window renderizing
window.onload = function()
{
    // Game division
    var div_game = document.getElementById('div_game')
    div_game.appendChild(grid);

    // Button division
    var div_buttons = document.getElementById('div_buttons')
    var start_button = document.getElementById('start_button')
    var stop_button = document.getElementById('stop_button')
    var interval = null;

    start_button.disabled = false;
    stop_button.disabled = true;
}

</script>
</head>

<body>

<div id="div_game" class="game_class"> </div>

<div id="div_buttons" class="buttons_class">
    <button id="start_button" onclick="startGame()">START</button>
    <button id="stop_button" onclick="stopGame()">STOP</button>
    <br>
    <button onclick="randomState()">RANDOM</button>
    <button onclick="clearState()">CLEAR</button>
    <br>
</div>

<div id="div_credits" class="credits_class">
<br>
(Grid based on this <a href="http://stackoverflow.com/questions/9140101/creating-a-clickable-grid-in-a-web-browser">StackOverflow thread</a>.)
</div>

</body>

</html>