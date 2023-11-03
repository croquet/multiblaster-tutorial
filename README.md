# Croquet Multiblaster Tutorial

Each HTML file in this project contains an increasingly complex multiplayer game built using [Croquet](https://croquet.io/docs/).

## Step 0

This is a non-Croquet app. It shows a few asteroids floating through space.

If you run this in two windows, the asteroids will float differently.

## Step 1

This is the same app, but using a Croquet Model for the asteroids.

A session name and password is automatically appended to the URL.
If you open that session URL in another window or on another device,
the asteroids will float the same.

## Step 2

This step adds interactive space ships.

For each player joining, another spaceship is created.
It subscribes to that player's input only, using the player's `viewid` as an event scope.

Key up and down events of the arrow keys publish events to enable and disable the thrusters.

## Step 3

When pressing the space bar, a blaster event is published.

The ship subscribes to that event and creates a new blast that
moves in the direction of the ship, and destroys itself after a while.

## Step 4

In this the we add collision detection between the blasts and the asteroids.
When hit, Asteroids split into two smaller chunks, or are destroyed completely.

## Step 5

Add collision between ships and asteroids.
Turn both into debris that's floating for a while.

## Step 6

Add scoring for ships hitting an asteroid. Also, draw our own ship filled.

## Step 7

_yer to be written_

Add render smoothing for 60 fps animation.

## Step 8

_yet to be written_

Add persistent highscore.

## Step 9

This is the finished tutorial game.

## Full Game

There's an even more polished game with some gimmicks at
https://github.com/croquet/multiblaster/

You can play it online at https://croquet.io/multiblaster/