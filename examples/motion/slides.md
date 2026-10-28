---
markdownpoint: "1.0"
id: 3f6b2a9e-8c41-4d7b-9a35-6e0d2c7f1b84
---

<!-- slide: curves -->

<!-- text: heading -->
Curves==.==

<!-- text: label-linear -->
linear

<!-- text: label-smooth -->
smooth

<!-- text: label-ease-in-out -->
ease-in-out

<!-- text: label-anticipate -->
anticipate

<!-- text: label-anticipate-overshoot -->
anticipate-overshoot

<!-- text: label-bounce -->
bounce

<!-- text: label-elastic -->
elastic

<!-- text: caption -->
On a click, each dot crosses on its own curve.

<!-- notes -->
Seven dots cross the slide on one click, each keyframe naming its curve in `interpolation`. The top dot's keyframe names none, so it moves at a constant speed, linear. The slide leaves with a push upward, its transition's `direction`.

<!-- slide: settings -->

<!-- text: heading -->
Curves with settings==.==

<!-- text: label-overshoot -->
overshoot, back 3

<!-- text: label-bounce -->
bounce, bounciness 0.8

<!-- text: label-elastic -->
elastic, amplitude 1.5, period 0.5

<!-- text: label-spring -->
spring, amount 0.4, response 0.6

<!-- text: caption -->
Settings change how far a curve pulls back, swings past or bounces.

<!-- notes -->
A curve with settings is a record that names its `curve`. `back` sets how far overshoot swings past, `bounciness` how high a bounce rebounds, `amplitude` and `period` an elastic's swing, and `amount` and `response` a spring's.

<!-- slide: keyframes -->

<!-- text: heading -->
Keyframes==.==

<!-- text: label-loop -->
A loop

<!-- text: label-wave -->
Waves

<!-- text: label-zoom -->
An even zoom

<!-- text: label-turn -->
A turn back

<!-- text: caption -->
The card loops, the dots bob, the oval zooms evenly and the last dot turns back halfway.

<!-- notes -->
The card slides in past its place, settles, and then swings right and back for as long as the slide shows. Its curve's `loop` repeats the keyframes `from` the second one on, so the slide-in plays once. The two dots bob on waves from `oscillate`, a triangle wave that starts a quarter of the way into its cycle and a noise wave from seed 7. The oval starts at `scale` 0.5 by 0.25 and grows fourfold, `exponential-scale` keeping the zoom even. The last dot heads for the far side, but its keyframe's `incoming` cuts that longer move off halfway, and it turns back. The slide leaves through the accent color, its transition's `color`.

<!-- slide: words -->

<!-- text: heading -->
Words in pieces==.==

<!-- text: body -->
These letters arrive one at a time, in a shuffled order.

This paragraph waits a moment before its turn.

<!-- text: caption -->
These letters start with their exit already played.

The second click plays it backward.

<!-- notes -->
The bar is revealed behind a wipe as its `progress` runs from 0 to 1. The body's letters enter one at a time in a random order from `seed` 7, sliding in from the left, with each space taking a turn (`spaces`) and a `pause` between paragraphs. Its `progress` starts at 0 and the first click runs it to 1. The caption starts with its exit at `progress` 1, gone. The second click runs the exit back to 0, so its letters turn and sharpen into place.

<!-- slide: copies -->

<!-- text: heading -->
Copies of one shape==.==

<!-- text: caption -->
Eight copies turn in a ring, each a little later than the one before.

<!-- notes -->
One square makes eight copies with `repeater`, each turned 45 degrees about a point below it and a little smaller. The last copy fades to `end-opacity` 0.2. Each copy turns, scales and waits a little at random (`rotation-randomness`, `scale-randomness`, `stagger-randomness`), from `seed` 5. The click turns the square, and each copy follows 0.08 seconds after the one before. The slide leaves with a carry-over that matches words, so the words the two headings share move to their places on the next slide.

<!-- slide: arrival -->

<!-- text: heading -->
Eight copies of one shape==.==

<!-- text: caption -->
The words both headings share carry over from the slide before.

<!-- notes -->
The previous slide's carry-over matches the two headings word by word: "of one shape" moves to its place in this heading, "Copies" fades out and "Eight copies" fades in. The square has the same id on both slides and stands where the click left it, turned 90 degrees, so its copies stay still while the words move.

<!-- slide: moments -->

<!-- text: heading -->
One composition at four moments==.==

<!-- text: early-label -->
0.5 seconds

<!-- text: middle-label -->
1 second

<!-- text: late-label -->
1.5 seconds

<!-- text: end-label -->
The last moment

<!-- notes -->
The sweep composition moves its dot along the track in 2 seconds. Each instance's `time-remap` holds it at one moment of that move. The fourth leaves `time-remap` out, so it shows the last moment. A step can key `time-remap` to play the composition at any speed.

<!-- composition: sweep -->
