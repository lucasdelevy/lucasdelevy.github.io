---
layout: page
permalink: /game-of-life/
---

<html>

<head>
<style>
/* Intro div*/
.intro-class
{
    text-align: center;
}

body
{
    background-color: #FFFFFF;
}

/* Game div */
:root
{
    --dead-color: #FFD8D8;
    --alive-color: #FF8686;
}
.game-class
{
    align: center;
}

/* Round grid */
.round-grid
{
    margin: 0vw auto;
    border-collapse: separate;
    border-spacing: 0.00vw;
}
.round-grid td
{
    background-clip: padding-box;
    border-radius: 5vw;
    background-color: var(--dead-color);
    color: var(--dead-color);
    border: 0.14vw solid var(--dead-color);
}
.round-grid td.clicked
{
    border-color: var(--alive-color);
    background-color: var(--alive-color);
}

/* Square grid */
.square-grid
{
    margin: 0vw auto;
    border-collapse: collapse;
    border-spacing: 0.08vw;
}
.square-grid td
{
    width: 0.12vw;
    height: 0.12vw;
    border: 0.10vw solid var(--dead-color);
}
.square-grid td.clicked
{
    background-color: var(--alive-color);
}

/* Settings div*/
.settings_div
{
    position: relative;
    overflow: hidden;
    width: all;
    height: 130vw;
}
.action-buttons-div
{
    text-align: right;
    float: right;
    width: 50%;
    display: inline;
    line-height: 4px;
}
.pattern-buttons-div
{
    text-align: left;
    float: left;
    width: 50%;
    line-height: 4px;
}
.nice-buttons
{
    background-color: #3E8181;
    color: white;
    padding: 1vw;
    font-size: 16px;
    border: none;
    cursor: pointer;
    display: auto;
}
.hidden-nice-buttons
{
    background-color: #7DA234;
    color: white;
    padding: 1vw;
    font-size: 16px;
    border: none;
    cursor: pointer;
    display: auto;
}
.nice-buttons:hover, .nice-buttons:focus
{
    background-color: #216868;
}
.nice-buttons:disabled
{
    background-color: #97C2C2;
    cursor: default;
}
.buttons-hidden-view
{
    position: relative;
    text-align: center;
    align-content: left;
    display: none;
    line-height: 4px;
}

/* Credits div*/
.credits-class
{
    position: relative;
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
var window_width = Math.max(document.documentElement.clientWidth, window.innerWidth || 0)
var window_height = Math.max(document.documentElement.clientHeight, window.innerHeight || 0)

var ncols = window_width/8;
var nrows = window_height/8;

var grid = clickableGrid(nrows,ncols,
    function(el,row,col,i)
    {
        el.className = el.className == 'clicked' ? 'unclicked' : 'clicked'
    });
var old_grid = clickableGrid(nrows,ncols,
    function(el,row,col,i)
    {
        el.className = el.className == 'clicked' ? 'unclicked' : 'clicked'
    });
grid.className = 'square-grid'
old_grid.className = 'square-grid'

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

function getLivingNeighbors(this_grid, index_i, index_j)
{
    var living = 0;

    for(var i = -1; i < 2; i++)
    {
        for(var j = -1; j < 2; j++)
        {
            if (i == 0 && j == 0)
                continue;

            these_rows = this_grid.rows[index_i+i]

            if(these_rows == undefined)
                continue;
            
            cell = these_rows.cells[index_j+j];

            if(cell == undefined)
                continue;
            
            if(cell.className == 'clicked')
                living++;
        }
    }

    return living
}

// Game functions
var iteraction = 0;
function playSimulation()
{
    old_grid.innerHTML = grid.innerHTML;

    for(var i = 0; i < nrows; i++)
    {
        for (var j = 0; j < ncols; j++)
        {
            game_cell = grid.rows[i].cells[j];

            // Check how many neighbors are alive
            living = getLivingNeighbors(old_grid, i, j);

            // GAME OF LIFE
            // From: https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life
            // Obs.: Unclicked aka dead, clicked aka alive
            // 1. Any live cell with fewer than two live neighbours dies, as if caused by under-population.
            if(game_cell.className == 'clicked')
            {
                if(living < 2)
                    game_cell.className = 'unclicked';
            // 2. Any live cell with more than three live neighbours dies, as if by over-population.
                if(living > 3)
                    game_cell.className = 'unclicked'
            // 3. Any live cell with two or three live neighbours lives on to the next generation.
            }
            // 4. Any dead cell with exactly three live neighbours becomes a live cell, as if by reproduction.
            if(game_cell.className == 'unclicked' && living == 3)
                game_cell.className = 'clicked';
        }
    }

    iteraction++;
}

function startGame()
{
    var i = 0;
    start_button.disabled = true;
    stop_button.disabled = false;

    var sim_period = document.getElementById("sim_period_slider").value
    interval = setInterval(playSimulation, sim_period)
}

function stopGame()
{
    start_button.disabled = false;
    stop_button.disabled = true;
    clearInterval(interval);
}

function stepGame()
{
    playSimulation();
}

function restartGame()
{
    if (start_button.disabled)
    {
        stopGame();
        startGame();
    }
}

function changeGridStyle()
{
    grid.className = grid.className == 'square-grid' ? 'round-grid' : 'square-grid';
}

// Patterns

function randomState()
{
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
    for(var i = 0; i < nrows; i++)
    {
        for (var j = 0; j < ncols; j++)
        {
            random_cell = grid.rows[i].cells[j];
            random_cell.className = 'unclicked';
        }
    }
}

function stillLifeState()
{
    clearState();

    // 4px square block
    random_i = Math.round(Math.random()*(nrows-2))
    random_j = Math.round(Math.random()*(ncols-2))

    grid.rows[random_i].cells[random_j].className = 'clicked';
    grid.rows[random_i+1].cells[random_j].className = 'clicked';
    grid.rows[random_i].cells[random_j+1].className = 'clicked';
    grid.rows[random_i+1].cells[random_j+1].className = 'clicked';

    // Beehive
    random_i = Math.round(Math.random()*(nrows-4))
    random_j = Math.round(Math.random()*(ncols-3))    

    grid.rows[random_i].cells[random_j+1].className = 'clicked';
    grid.rows[random_i].cells[random_j+2].className = 'clicked';
    grid.rows[random_i+1].cells[random_j].className = 'clicked';
    grid.rows[random_i+1].cells[random_j+3].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+1].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+2].className = 'clicked';

    // Loaf
    random_i = Math.round(Math.random()*(nrows-4))
    random_j = Math.round(Math.random()*(ncols-4))

    grid.rows[random_i].cells[random_j+1].className = 'clicked';
    grid.rows[random_i].cells[random_j+2].className = 'clicked';
    grid.rows[random_i+1].cells[random_j].className = 'clicked';
    grid.rows[random_i+1].cells[random_j+3].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+1].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+3].className = 'clicked';
    grid.rows[random_i+3].cells[random_j+2].className = 'clicked';

    // Boat
    random_i = Math.round(Math.random()*(nrows-4))
    random_j = Math.round(Math.random()*(ncols-4))

    grid.rows[random_i].cells[random_j].className = 'clicked';
    grid.rows[random_i].cells[random_j+1].className = 'clicked';
    grid.rows[random_i+1].cells[random_j].className = 'clicked';
    grid.rows[random_i+1].cells[random_j+2].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+1].className = 'clicked';
}

function oscillatorState()
{
    clearState();

    // Blinker
    random_i = Math.round(Math.random()*(nrows-3))
    random_j = Math.round(Math.random()*(ncols-3))

    grid.rows[random_i].cells[random_j].className = 'clicked';
    grid.rows[random_i+1].cells[random_j].className = 'clicked';
    grid.rows[random_i+2].cells[random_j].className = 'clicked';

    // Toad
    random_i = Math.round(Math.random()*(nrows-4))
    random_j = Math.round(Math.random()*(ncols-4))    

    grid.rows[random_i+1].cells[random_j+1].className = 'clicked';
    grid.rows[random_i+1].cells[random_j+2].className = 'clicked';
    grid.rows[random_i+1].cells[random_j+3].className = 'clicked';
    grid.rows[random_i+2].cells[random_j].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+1].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+2].className = 'clicked';

    // Beacon
    random_i = Math.round(Math.random()*(nrows-4))
    random_j = Math.round(Math.random()*(ncols-4))

    grid.rows[random_i].cells[random_j].className = 'clicked';
    grid.rows[random_i].cells[random_j+1].className = 'clicked';
    grid.rows[random_i+1].cells[random_j].className = 'clicked';
    grid.rows[random_i+1].cells[random_j+1].className = 'clicked';
    
    grid.rows[random_i+2].cells[random_j+2].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+3].className = 'clicked';
    grid.rows[random_i+3].cells[random_j+2].className = 'clicked';
    grid.rows[random_i+3].cells[random_j+3].className = 'clicked';

    // Pulsar
    random_i = Math.round(Math.random()*(nrows-13))
    random_j = Math.round(Math.random()*(ncols-13))

    grid.rows[random_i].cells[random_j+2].className = 'clicked';
    grid.rows[random_i].cells[random_j+3].className = 'clicked';
    grid.rows[random_i].cells[random_j+4].className = 'clicked';
    grid.rows[random_i].cells[random_j+8].className = 'clicked';
    grid.rows[random_i].cells[random_j+9].className = 'clicked';
    grid.rows[random_i].cells[random_j+10].className = 'clicked';

    grid.rows[random_i+2].cells[random_j].className = 'clicked';
    grid.rows[random_i+3].cells[random_j].className = 'clicked';
    grid.rows[random_i+4].cells[random_j].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+5].className = 'clicked';
    grid.rows[random_i+3].cells[random_j+5].className = 'clicked';
    grid.rows[random_i+4].cells[random_j+5].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+7].className = 'clicked';
    grid.rows[random_i+3].cells[random_j+7].className = 'clicked';
    grid.rows[random_i+4].cells[random_j+7].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+12].className = 'clicked';
    grid.rows[random_i+3].cells[random_j+12].className = 'clicked';
    grid.rows[random_i+4].cells[random_j+12].className = 'clicked';

    grid.rows[random_i+5].cells[random_j+2].className = 'clicked';
    grid.rows[random_i+5].cells[random_j+3].className = 'clicked';
    grid.rows[random_i+5].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+5].cells[random_j+8].className = 'clicked';
    grid.rows[random_i+5].cells[random_j+9].className = 'clicked';
    grid.rows[random_i+5].cells[random_j+10].className = 'clicked';

    grid.rows[random_i+7].cells[random_j+2].className = 'clicked';
    grid.rows[random_i+7].cells[random_j+3].className = 'clicked';
    grid.rows[random_i+7].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+7].cells[random_j+8].className = 'clicked';
    grid.rows[random_i+7].cells[random_j+9].className = 'clicked';
    grid.rows[random_i+7].cells[random_j+10].className = 'clicked';

    grid.rows[random_i+8].cells[random_j].className = 'clicked';
    grid.rows[random_i+9].cells[random_j].className = 'clicked';
    grid.rows[random_i+10].cells[random_j].className = 'clicked';
    grid.rows[random_i+8].cells[random_j+5].className = 'clicked';
    grid.rows[random_i+9].cells[random_j+5].className = 'clicked';
    grid.rows[random_i+10].cells[random_j+5].className = 'clicked';
    grid.rows[random_i+8].cells[random_j+7].className = 'clicked';
    grid.rows[random_i+9].cells[random_j+7].className = 'clicked';
    grid.rows[random_i+10].cells[random_j+7].className = 'clicked';
    grid.rows[random_i+8].cells[random_j+12].className = 'clicked';
    grid.rows[random_i+9].cells[random_j+12].className = 'clicked';
    grid.rows[random_i+10].cells[random_j+12].className = 'clicked';

    grid.rows[random_i+12].cells[random_j+2].className = 'clicked';
    grid.rows[random_i+12].cells[random_j+3].className = 'clicked';
    grid.rows[random_i+12].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+12].cells[random_j+8].className = 'clicked';
    grid.rows[random_i+12].cells[random_j+9].className = 'clicked';
    grid.rows[random_i+12].cells[random_j+10].className = 'clicked';

    // Pentadecathlon
    random_i = Math.round(Math.random()*(nrows-16))
    random_j = Math.round(Math.random()*(ncols-9))

    grid.rows[random_i+3].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+3].cells[random_j+5].className = 'clicked';
    grid.rows[random_i+3].cells[random_j+6].className = 'clicked';
    grid.rows[random_i+4].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+4].cells[random_j+6].className = 'clicked';
    grid.rows[random_i+5].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+5].cells[random_j+5].className = 'clicked';
    grid.rows[random_i+5].cells[random_j+6].className = 'clicked';
    grid.rows[random_i+6].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+6].cells[random_j+5].className = 'clicked';
    grid.rows[random_i+6].cells[random_j+6].className = 'clicked';
    grid.rows[random_i+7].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+7].cells[random_j+5].className = 'clicked';
    grid.rows[random_i+7].cells[random_j+6].className = 'clicked';
    grid.rows[random_i+8].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+8].cells[random_j+5].className = 'clicked';
    grid.rows[random_i+8].cells[random_j+6].className = 'clicked';
    grid.rows[random_i+9].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+9].cells[random_j+6].className = 'clicked';
    grid.rows[random_i+10].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+10].cells[random_j+5].className = 'clicked';
    grid.rows[random_i+10].cells[random_j+6].className = 'clicked';
}

function spaceshipsState()
{
    clearState();

    // Glider
    random_i = Math.round(Math.random()*(nrows-3))
    random_j = Math.round(Math.random()*(ncols-3))

    grid.rows[random_i].cells[random_j+1].className = 'clicked';
    grid.rows[random_i+1].cells[random_j+2].className = 'clicked';
    grid.rows[random_i+2].cells[random_j].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+1].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+2].className = 'clicked';
    

    // Lightweight spaceship (LWSS)
    random_i = Math.round(Math.random()*(nrows-4))
    random_j = Math.round(Math.random()*(ncols-5))    

    grid.rows[random_i].cells[random_j+1].className = 'clicked';
    grid.rows[random_i].cells[random_j+2].className = 'clicked';
    grid.rows[random_i].cells[random_j+3].className = 'clicked';
    grid.rows[random_i].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+1].cells[random_j].className = 'clicked';
    grid.rows[random_i+1].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+2].cells[random_j+4].className = 'clicked';
    grid.rows[random_i+3].cells[random_j].className = 'clicked';
    grid.rows[random_i+3].cells[random_j+3].className = 'clicked';
}

function rPentominoState()
{
    clearState();

    central_i = Math.round(nrows/2-1);
    central_j = Math.round(ncols/2-1);

    grid.rows[central_i].cells[central_j+1].className = 'clicked';
    grid.rows[central_i].cells[central_j+2].className = 'clicked';
    grid.rows[central_i+1].cells[central_j].className = 'clicked';
    grid.rows[central_i+1].cells[central_j+1].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+1].className = 'clicked';
}

function diehardState()
{
    clearState();

    central_i = Math.round(nrows/2-2);
    central_j = Math.round(ncols/2-4);

    grid.rows[central_i].cells[central_j+6].className = 'clicked';
    grid.rows[central_i+1].cells[central_j].className = 'clicked';
    grid.rows[central_i+1].cells[central_j+1].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+1].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+5].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+6].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+7].className = 'clicked';
}

function acornState()
{
    clearState();

    central_i = Math.round(nrows/2-2);
    central_j = Math.round(ncols/2-4);

    grid.rows[central_i].cells[central_j+1].className = 'clicked';
    grid.rows[central_i+1].cells[central_j+3].className = 'clicked';
    grid.rows[central_i+2].cells[central_j].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+1].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+4].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+5].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+6].className = 'clicked';
}

function gosperGliderGunState()
{
    clearState();

    central_i = 1;
    central_j = 1;

    grid.rows[central_i].cells[central_j+24].className = 'clicked';
    grid.rows[central_i+1].cells[central_j+22].className = 'clicked';
    grid.rows[central_i+1].cells[central_j+24].className = 'clicked';

    grid.rows[central_i+2].cells[central_j+12].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+13].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+20].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+21].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+34].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+35].className = 'clicked';

    grid.rows[central_i+3].cells[central_j+11].className = 'clicked';
    grid.rows[central_i+3].cells[central_j+15].className = 'clicked';
    grid.rows[central_i+3].cells[central_j+20].className = 'clicked';
    grid.rows[central_i+3].cells[central_j+21].className = 'clicked';
    grid.rows[central_i+3].cells[central_j+34].className = 'clicked';
    grid.rows[central_i+3].cells[central_j+35].className = 'clicked';

    grid.rows[central_i+4].cells[central_j].className = 'clicked';
    grid.rows[central_i+4].cells[central_j+1].className = 'clicked';
    grid.rows[central_i+4].cells[central_j+10].className = 'clicked';
    grid.rows[central_i+4].cells[central_j+16].className = 'clicked';
    grid.rows[central_i+4].cells[central_j+20].className = 'clicked';
    grid.rows[central_i+4].cells[central_j+21].className = 'clicked';

    grid.rows[central_i+5].cells[central_j].className = 'clicked';
    grid.rows[central_i+5].cells[central_j+1].className = 'clicked';
    grid.rows[central_i+5].cells[central_j+10].className = 'clicked';
    grid.rows[central_i+5].cells[central_j+14].className = 'clicked';
    grid.rows[central_i+5].cells[central_j+16].className = 'clicked';
    grid.rows[central_i+5].cells[central_j+17].className = 'clicked';
    grid.rows[central_i+5].cells[central_j+22].className = 'clicked';
    grid.rows[central_i+5].cells[central_j+24].className = 'clicked';

    grid.rows[central_i+6].cells[central_j+10].className = 'clicked';
    grid.rows[central_i+6].cells[central_j+16].className = 'clicked';
    grid.rows[central_i+6].cells[central_j+24].className = 'clicked';

    grid.rows[central_i+7].cells[central_j+11].className = 'clicked';
    grid.rows[central_i+7].cells[central_j+15].className = 'clicked';

    grid.rows[central_i+8].cells[central_j+12].className = 'clicked';
    grid.rows[central_i+8].cells[central_j+13].className = 'clicked';

}

function minimalInfinite()
{
    clearState();

    central_i = Math.round(nrows/2-3);
    central_j = Math.round(ncols/2-4);

    grid.rows[central_i].cells[central_j+6].className = 'clicked';
    grid.rows[central_i+1].cells[central_j+4].className = 'clicked';
    grid.rows[central_i+1].cells[central_j+6].className = 'clicked';
    grid.rows[central_i+1].cells[central_j+7].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+4].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+6].className = 'clicked';
    grid.rows[central_i+3].cells[central_j+4].className = 'clicked';
    grid.rows[central_i+4].cells[central_j+2].className = 'clicked';
    grid.rows[central_i+5].cells[central_j].className = 'clicked';
    grid.rows[central_i+5].cells[central_j+2].className = 'clicked';
}

function smallestInfinite()
{
    clearState();

    central_i = Math.round(nrows/2-2);
    central_j = Math.round(ncols/2-2);

    grid.rows[central_i].cells[central_j].className = 'clicked';
    grid.rows[central_i].cells[central_j+1].className = 'clicked';
    grid.rows[central_i].cells[central_j+2].className = 'clicked';
    grid.rows[central_i].cells[central_j+4].className = 'clicked';
    grid.rows[central_i+1].cells[central_j].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+3].className = 'clicked';
    grid.rows[central_i+2].cells[central_j+4].className = 'clicked';
    grid.rows[central_i+3].cells[central_j+1].className = 'clicked';
    grid.rows[central_i+3].cells[central_j+2].className = 'clicked';
    grid.rows[central_i+3].cells[central_j+4].className = 'clicked';
    grid.rows[central_i+4].cells[central_j].className = 'clicked';
    grid.rows[central_i+4].cells[central_j+2].className = 'clicked';
    grid.rows[central_i+4].cells[central_j+4].className = 'clicked';

}

function oneRowInfinite()
{
    clearState();

    central_i = Math.round(nrows/2-2)
    central_j = Math.round(ncols/2-20)

    grid.rows[central_i].cells[central_j].className = 'clicked';
    grid.rows[central_i].cells[central_j+1].className = 'clicked';
    grid.rows[central_i].cells[central_j+2].className = 'clicked';
    grid.rows[central_i].cells[central_j+3].className = 'clicked';
    grid.rows[central_i].cells[central_j+4].className = 'clicked';
    grid.rows[central_i].cells[central_j+5].className = 'clicked';
    grid.rows[central_i].cells[central_j+6].className = 'clicked';
    grid.rows[central_i].cells[central_j+7].className = 'clicked';
    grid.rows[central_i].cells[central_j+9].className = 'clicked';
    grid.rows[central_i].cells[central_j+10].className = 'clicked';
    grid.rows[central_i].cells[central_j+11].className = 'clicked';
    grid.rows[central_i].cells[central_j+12].className = 'clicked';
    grid.rows[central_i].cells[central_j+13].className = 'clicked';
    grid.rows[central_i].cells[central_j+17].className = 'clicked';
    grid.rows[central_i].cells[central_j+18].className = 'clicked';
    grid.rows[central_i].cells[central_j+19].className = 'clicked';
    grid.rows[central_i].cells[central_j+26].className = 'clicked';
    grid.rows[central_i].cells[central_j+27].className = 'clicked';
    grid.rows[central_i].cells[central_j+28].className = 'clicked';
    grid.rows[central_i].cells[central_j+29].className = 'clicked';
    grid.rows[central_i].cells[central_j+30].className = 'clicked';
    grid.rows[central_i].cells[central_j+31].className = 'clicked';
    grid.rows[central_i].cells[central_j+32].className = 'clicked';
    grid.rows[central_i].cells[central_j+34].className = 'clicked';
    grid.rows[central_i].cells[central_j+35].className = 'clicked';
    grid.rows[central_i].cells[central_j+36].className = 'clicked';
    grid.rows[central_i].cells[central_j+37].className = 'clicked';
    grid.rows[central_i].cells[central_j+38].className = 'clicked';
}

/* When the user clicks on the button, 
  toggle between hiding and showing the dropdown content */
function showSimplePatterns()
{
    document.getElementById("simple_patterns_button").style.display = 'none';
    document.getElementById("simple_patterns_div").style.display = 'initial';
}

function hideSimplePatterns()
{
    document.getElementById("simple_patterns_button").style.display = 'initial';
    document.getElementById("simple_patterns_div").style.display = 'none';
}

function showMethuselahsPatterns()
{
    document.getElementById("methuselahs_patterns_button").style.display = 'none';
    document.getElementById("methuselahs_patterns_div").style.display = 'initial';
}

function hideMethuselahsPatterns()
{
    document.getElementById("methuselahs_patterns_button").style.display = 'initial';
    document.getElementById("methuselahs_patterns_div").style.display = 'none';
}

function showOtherPatterns()
{
    document.getElementById("other_patterns_button").style.display = 'none';
    document.getElementById("other_patterns_div").style.display = 'initial';
}

function hideOtherPatterns()
{
    document.getElementById("other_patterns_button").style.display = 'initial';
    document.getElementById("other_patterns_div").style.display = 'none';
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

<div id="div_intro" class="intro-class">
A simple demo of Conway's Game of Life.
</div>
<br>
<div id="div_game" class="game-class">
</div>
<br>
<div class="settings_div">
    <div id="pattern_buttons_div" class="pattern-buttons-div">
        <div id="simple_buttons_div" class="buttons-div">
            <button id="simple_patterns_button" onmouseenter="showSimplePatterns()" class="nice-buttons">Simple patterns</button>

            <div id="simple_patterns_div" onmouseleave="hideSimplePatterns()" class="buttons-hidden-view">
                <button class="hidden-nice-buttons" onclick="stillLifeState()">Still Life</button>
                <button class="hidden-nice-buttons" onclick="oscillatorState()">Oscillators</button>
                <button class="hidden-nice-buttons" onclick="spaceshipsState()">Spaceships</button>
            </div>
        </div>
        <br>
        <div id="methuselahs_buttons_div" class="buttons-div">
            <button id="methuselahs_patterns_button" onmouseenter="showMethuselahsPatterns()" class="nice-buttons">Methuselahs patterns</button>
            
            <div id="methuselahs_patterns_div" onmouseleave="hideMethuselahsPatterns()" class="buttons-hidden-view">
                <button class="hidden-nice-buttons" onclick="rPentominoState()">The R-pentomino</button>
                <button class="hidden-nice-buttons" onclick="diehardState()">Diehard</button>
                <button class="hidden-nice-buttons" onclick="acornState()">Acorn</button>
            </div>
        </div>
        <br>
        <div id="other_buttons_div" class="buttons-div">
            <button id="other_patterns_button" onmouseenter="showOtherPatterns()" class="nice-buttons">Other patterns</button>
            
            <div id="other_patterns_div" onmouseleave="hideOtherPatterns()" class="buttons-hidden-view">
                <button class="hidden-nice-buttons" onclick="gosperGliderGunState()">Gosper Glider Gun</button>
                <button class="hidden-nice-buttons" onclick="minimalInfinite()">Minimal Cells Infinite*</button>
                <br>
                <button class="hidden-nice-buttons" onclick="smallestInfinite()">Smallest Area Infinite*</button>
                <button class="hidden-nice-buttons" onclick="oneRowInfinite()">One Row Infinite*</button>
            </div>
        </div>
    </div>

    <div id="action_buttons_div" class="action-buttons-div">
        <button class="nice-buttons" id="start_button" onclick="startGame()">START</button>
        <button class="nice-buttons" id="stop_button" onclick="stopGame()">STOP</button>
        <button class="nice-buttons" id="step_button" onclick="stepGame()">STEP</button>
        <br>
        <br>
        <button class="nice-buttons" onclick="randomState()">RANDOM</button>
        <button class="nice-buttons" onclick="clearState()">CLEAR</button>
        <button class="nice-buttons" id="style_button" onclick="changeGridStyle()">CSS</button>
        <br>
        <br>
        <br>
        fast <input type="range" id="sim_period_slider" value="100" max="1000" min="50" onchange="restartGame()"> slow
    </div>
</div>

<br>

<div id="credits_div" class="credits-class">
    * Does not continue infinitely due to grid size limitation.
    <br>
    (Grid based on <a href="http://stackoverflow.com/questions/9140101/creating-a-clickable-grid-in-a-web-browser">this StackOverflow thread</a>.)
    <br>
    (Rules are from <a href="https://en.wikipedia.org/wiki/Conway%27s_Game_of_Life">the Wikipedia article</a>.)
</div>

</body>

</html>