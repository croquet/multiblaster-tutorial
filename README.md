# Croquet Multiblaster Tutorial

Each HTML file in [this repository](https://github.com/croquet/multiblaster-tutorial/)
contains an increasingly complete multiplayer game built using [Croquet](https://croquet.io/docs/).

It's a 2D game, and its visuals are intentionally kept simple so that the code is more understandable.

The game has asteroids and ships floating in space.
If an objects goes beyond the screen edge, it comes back in on the other side.
Players steer their ships by firing thrusters (left, right and forward).
They can also shoot blasts which cause asteroids to break up and vanish.
Successful blasts increase the player's score, while colliding with an asteroid
causes a ship to be destroyed and lose all points.

## Step 0: Asteroids floating without Croquet

([source](https://github.com/croquet/multiblaster-tutorial/blob/main/step0.html))
      ([run](https://croquet.github.io/multiblaster-tutorial/step0.html))

This is a non-Croquet app. It shows a few asteroids floating through space.
If you run this in two windows, the asteroids will float differently.

The playfield is a 1000⨉1000 canvas scaled to fit in the window.
Asteroids are drawn using plain white strokes.

Each asteroid has an `x` and `y` position as well as an `a` angle.
Additionally, it has `dx`, `dy` and `da` properties which represent the
amount to change the position and angle in every time step to
animate the object in its `move()` method.

The movement is restricted to the playfield's size (0 to 1000) using remainder math (the `%` operator).
This causes the asteroid to come back in on the other side when it floats out.

This file has about 80 lines of code total.

## Step 1: Asteroids synchronized with Croquet

([source](https://github.com/croquet/multiblaster-tutorial/blob/main/step1.html))
      ([run](https://croquet.github.io/multiblaster-tutorial/step1.html))

This is the same app, but using a Croquet Model for the asteroids.

A session name and password is automatically appended to the URL.
If you open that session URL in another window or on another device,
the asteroids will float exactly the same in both.

The app is devided into two parts: The "model" is the part that is synchronized
by Croquet for all users. It is like a shared computer that all users directly
interact with. The other part is the "view", which displays the model to the user
by drawing the asteroids on a canvas.

The last few lines instruct Croquet to join a session for a particular model and view class.
It also needs an API key. You should fetch your own key from https://croquet.io/keys

This version has only 20 lines more than the non-Croquet one from step 0.

Notice that the computation looks exactly the same, no special data structures need to be used,
all models are synchronized between machines without any special markup.

The only new construct is the line

    this.future(50).move();

inside of the `move()` method. This causes `move()` to be called again 50 ms in the future,
similarly to the timeout in step 0. Future messages are how you define an object's behavior over
time in Croquet.

Also notice that the view's `update()` method can read the asteroid positions directly from the model
for drawing. Unlike in server-client computing, these positions do not need to be transmitted
via the network. They are already available locally.

However, you must take care to not accidentally modify any model properties directly,
because that would break the synchronization. See the next step for how to interact with the model.

## Step 2: Spaceships controlled by players

([source](https://github.com/croquet/multiblaster-tutorial/blob/main/step2.html))
      ([run](https://croquet.github.io/multiblaster-tutorial/step2.html))

This step adds interactive space ships.

For each player joining, another spaceship is created by subscribing to the session's
`view-join` and `view-exit` events.
Each ship subscribes to that player's input only, using the player's `viewid` as an event scope.
This is how the shared model can distinguish events sent from different user's views.

Key up and down events of the arrow keys publish events to enable and disable the thrusters.
The ships `move()` method uses the stored thruster values to accelerate or rotate the ship.

Publish and subscribe are used mainly to communicate events from the user's view to the shared model,
typically derived from user input. Unlike in other pub/sub systems you may be familiar with,
Croquet's pub/sub is not used to synchronize changed values or to communicate between different devices.
All communication is only between the local view and the shared model.

Before joining the session, `makeWidgetDock()` enables a QR code widget in the lower left corner.
This allows you to join the same session not only by copying the session URL but also by scanning
this code with a mobile device.

## Step 3: Firing a blaster

([source](https://github.com/croquet/multiblaster-tutorial/blob/main/step3.html))
      ([run](https://croquet.github.io/multiblaster-tutorial/step3.html))

When pressing the space bar, a blaster event is published.

The ship subscribes to that event and creates a new blast that
moves in the direction of the ship, and destroys itself after a while
by counting its `t` property up in every move step.

The blast registers itself with the game object when created,
and removes itself when destroyed. This is accomplished by
accessing the `Game` as the well-known `modelRoot`.

    get game() { return this.wellKnownModel("modelRoot"); }

## Step 4: Break up asteroids when hit by blasts

([source](https://github.com/croquet/multiblaster-tutorial/blob/main/step4.html))
      ([run](https://croquet.github.io/multiblaster-tutorial/step4.html))

In this step the we add collision detection between the blasts and the asteroids.
When hit, Asteroids split into two smaller chunks, or are destroyed completely.

To make this simpler, the individual future messages are now replaced by a single `mainLoop()`
method which calls all `move()` methods and then checks for collisions.

When an asteroid was hit by a blast, it shrinks itself and changes direction perpendicular to the shot.
Also it creates another asteroid that goes into the opposite direction. This makes it appear as if
the asteroid broke into two pieces.

## Step 5: Turn ship into debris after colliding with asteroids

([source](https://github.com/croquet/multiblaster-tutorial/blob/main/step5.html))
      ([run](https://croquet.github.io/multiblaster-tutorial/step5.html))

Now we add collision between ships and asteroids, and turn both into debris which is floating for a while.

We do this by adding a `wasHit` property that normally is `0`, but gets set to `1` when hit.
It then starts counting up for each `move()` step just like the `t` property in the blast,
and after a cetrtain number of steps destroys the asteroid and resets the ship.

The drawing code in the view's `update()` takes this `wasHit` property to show an exploded
version of the asteroid or ship.

Also, while the ship's `wasHit` is non-zero, its `move()` method ignores the thruster controls,
and the blaster cannot be fired. This forces the player to wait until the ship is reset to the
center of the screen.

## Step 6: Score points when hitting an asteroid with a blast

([source](https://github.com/croquet/multiblaster-tutorial/blob/main/step6.html))
      ([run](https://croquet.github.io/multiblaster-tutorial/step6.html))

Add scoring for ships hitting an asteroid.
When a blast is fired, we store a reference to the ship in the blast.
When the blast hits an asteroid, the ship's `scored()` method is called, which increments its `score`.

The `update()` method displays each ship's score next to the ship.
Also, to better distinguish our own ship, we draw it filled.
We find our own ship by comparing its `viewId` to the local `viewId`.

## Step 7: View-side animation smoothing

([source](https://github.com/croquet/multiblaster-tutorial/blob/main/step7.html))
      ([run](https://croquet.github.io/multiblaster-tutorial/step7.html))

Add render smoothing for 60 fps animation. The models move at 20 fps (because of the 50 ms future send
in the main loop) but for smooth animation you typically want to animate at a higher fps.
While we could increase the model update rate, that would make the timing depend very much
on the steadiness of ticks from the reflector.
Instead, we do automatic in-betweening in the view by decoupling the rendering position from the
model position, and updating the render position "smoothly."

This step uses the exact same model code as in step 7, so you can actually run
both side-by-side with the same session name and password to see the difference.

## Step 8: Persistent table of highscores

Add persistent highscore.

This step adds a text input field for players' initials (emoji work too).
Its value is kept in `localStorage` so players only have to type it once.

A highscore table is added to the model, and persisted using `persistSession()` call.
Persisting means that the important session contents survives even if the model code changes,
which normally means it starts from scratch. If persisted data exists for a session that uses
the same name but new code, it will be passed into the root model's `init()` method.

## Step 9: Support for mobile etc.

([source](https://github.com/croquet/multiblaster-tutorial/blob/main/step9.html))
      ([run](https://croquet.github.io/multiblaster-tutorial/step9.html))

This is the finished tutorial game. It has some more features, like
* support for mobile devices via touch input
* ASDW keys in addition to arrow keys
* visible thrusters
* "wrapped" drawing so that objects are half-visible on both sides when crossing the screen edge
* prevents ships getting destroyed by an asteroid in the spawn position
* etc.

## Advanced Game

There's an even more polished game with some gimmicks at
https://github.com/croquet/multiblaster/

One of its gimmicks is that if the initials contain an emoji, it will be used for shooting.

You can play it online at https://croquet.io/multiblaster/