# Week 4: Finishing "Alien Shootz"

Last week, we learned how to make animations with sprite sheets, add collisions so they could hit each other, and handle those collisions to kill a character. You may have even taken it to the next level and made the alien respawn so you had a rudimentary game.

This week we're going to build it into a full game. To do that, we have to learn a couple more things.

- Make a grid of aliens and make them work as a group.
- Gradually destroy our bunkers and let stuff through. (maybe - if there's time to design)
- Bringing it all together

Before we continue, you may ask "what's with the name?" Well, just to be on the right side of the law, we couldn't call it "Space Invaders," since that's trademarked, and pretty much every synonym of "attack" had already been combined with the word "alien" for some game or movie title. So we did what you do on the internet... we misspelled a word. 

## Making a grid of aliens

The first thing we have to do to make it like Space Invaders is have a grid of aliens that march back and forth across the screen, periodically dropping bombs. When one side of the alien horde hits the side of the screen, they turn and march back the other way. If I recall correctly (because it has been 30+ years since I played), they'd even move down a little bit each time they hit each side.

The goal of the game was to kill all the aliens, not just before they hit you with a bomb, but before they reached the bottom and an alien collided with you.

So, let's go into the experiments folder and look at the grid.js file. We can look at what it does by running the grid.html file.

```javascript
var mgd = {}; // variable to hold "my game data" throughout
// setting our gameboard dimensions
mgd.MAXWIDTH = 1200; // set maximum width of game board in pixels;
mgd.MAXHEIGHT = 900; // set maximum height of game board in pixels;

// define our enemies grid...
mgd.grid =[
  {
    alias: 'alien', 
    width: 56,
    hgap: 22, 
    height: 56, 
    vgap: 25, 
    count: 10, 
    rows: 2, 
    speed: 12,
    sequence: [0, 1, 2, 3, 4, 3, 2, 1]
  },
  {
    alias: 'rollien', 
    width: 56, 
    hgap: 22, 
    height: 56, 
    vgap: 25, 
    count: 10, 
    rows: 2,  
    speed: 16,
    sequence: [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 9, 9, 9, 8, 7, 6, 5, 4, 3, 2, 1, 0, 0, 0]
  },
  {
    alias: 'saucer', 
    width: 56, 
    hgap: 22, 
    height: 32, 
    vgap: 25, 
    count: 10, 
    rows: 3, 
    speed: 10,
    sequence: [0, 1, 2, 3, 4]
  }
]

mgd.offsetx = 20;
mgd.offsety = 20;

var game = {} // will hold game object

window.onload = function(){
  game = new Phaser.Game(mgd.MAXWIDTH, mgd.MAXHEIGHT, Phaser.AUTO, '', { preload: preload, create: create});

  function preload(){
    // load our animated sprites
    game.load.spritesheet('saucer', "../assets/gameart/shipsheet.png", 56 , 32, 5, 0, 0);
    game.load.spritesheet('alien', "../assets/gameart/aliensheet.png", 56 , 56 , 5 , 0 , 0 );
    game.load.spritesheet('rollien', "../assets/gameart/rolliensheet.png", 56 , 56 , 10 , 0 , 0 );
  }



  function create(){
    mgd.aliens = game.add.group();
    
    // loop through our grid of aliens
    for(var i = 0; i < mgd.grid.length; i++){
      //get a new alien type
      var alien = mgd.grid[i];
      //loop for the number of columns
      for(var j = 0; j < alien.rows; j++) {
        //loop for the number of aliens to go in each column
        for(var k = 0; k < alien.count; k++) {
          //add an alien to the row, starting at the x offset
          var templien = mgd.aliens.create((mgd.offsetx + (k * (alien.width + alien.hgap))), mgd.offsety, alien.alias);
          templien.animations.add('walk', alien.sequence);
          templien.animations.play('walk', alien.speed, true);
        }
        // increase mgd.offsety
        mgd.offsety += (alien.height + alien.vgap);
      } 
    }    

    // set our minimum and maximum dimensions for the game, then use the
    // SHOW_ALL scale mode to keep the game scaled.
    game.scale.setMinMax(400, 400, mgd.MAXWIDTH, mgd.MAXHEIGHT)
    game.scale.scaleMode = Phaser.ScaleManager.SHOW_ALL;
  }

}

```






## Gradually destroying our bunkers (optional)

A great part of Space Invaders was that you had bunkers you could not only hide under, but you could make a thin channel through to shoot up at the aliens without exposing yourself too much. But the alien bombs slowly wore down the bunkers. 

Yes. per https://phaser.io/examples/v2/arcade-physics/group-vs-group we  can check overlap instead of collide. 

Background color: 