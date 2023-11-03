# Croquet Multiblaster Tutorial

Each HTML file in this project contains an increasingly complex multiplayer game built using [Croquet](https://croquet.io/docs/).

## Step 0

Code: [step0.html](step0.html) ([RUN](https://croquet.github.io/multiblaster-tutorial/step0.html))

This is a non-Croquet app. It shows a few asteroids floating through space.

If you run this in two windows, the asteroids will float differently.

## Step 1

Code: [step1.html](step1.html) ([RUN](https://croquet.github.io/multiblaster-tutorial/step1.html))

This is the same app, but using a Croquet Model for the asteroids.

A session name and password is automatically appended to the URL.
If you open that session URL in another window or on another device,
the asteroids will float the same.

## Step 2

Code: [step2.html](step2.html) ([RUN](https://croquet.github.io/multiblaster-tutorial/step2.html))

This step adds interactive space ships.

For each player joining, another spaceship is created.
It subscribes to that player's input only, using the player's `viewid` as an event scope.

Key up and down events of the arrow keys publish events to enable and disable the thrusters.

## Step 3

Code: [step3.html](step3.html) ([RUN](https://croquet.github.io/multiblaster-tutorial/step3.html))

When pressing the space bar, a blaster event is published.

The ship subscribes to that event and creates a new blast that
moves in the direction of the ship, and destroys itself after a while.

## Step 4

Code: [step4.html](step4.html) ([RUN](https://croquet.github.io/multiblaster-tutorial/step4.html))

In this the we add collision detection between the blasts and the asteroids.
When hit, Asteroids split into two smaller chunks, or are destroyed completely.

## Step 5

Code: [step5.html](step5.html) ([RUN](https://croquet.github.io/multiblaster-tutorial/step5.html))

Add collision between ships and asteroids.
Turn both into debris that's floating for a while.

## Step 6

Code: [step6.html](step6.html) ([RUN](https://croquet.github.io/multiblaster-tutorial/step6.html))

Add scoring for ships hitting an asteroid. Also, draw our own ship filled.

## Step 7

Code: [step7.html](step7.html) ([RUN](https://croquet.github.io/multiblaster-tutorial/step7.html))

Add render smoothing for 60 fps animation. The models move at 20 fps (because of the 50 ms future send
in the main loop) but for smooth animation you typically want to animate at a higher fps.
While we could increase the model update rate, that would make the timing depend very much
on the steadiness of ticks from the reflector.
Instead, we do automatic in-betweening in the view by decoupling the rendering position from the
model position, and updating the render position "smoothly."

This step uses the exact same model code as in step 7, so you can actually run
both side-by-side with the same session name and password to see the difference.

## Step 8

_yet to be written_

Add persistent highscore.

## Step 9

Code: [step9.html](step9.html) ([RUN](https://croquet.github.io/multiblaster-tutorial/step9.html))

This is the finished tutorial game.
It has some more features, like
* support for mobile devices via touch input
* "wrapped" drawing so that objects are half-visible on both sides when crossing the screen edge
* prevents ships getting destroyed by an asteroid in the spawn position
* etc.

## Full Game

There's an even more polished game with some gimmicks at
https://github.com/croquet/multiblaster/

You can play it online at https://croquet.io/multiblaster/