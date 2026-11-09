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
Seven dots cross the slide on one click. Each keyframe names its curve in `interpolation`. The top dot's keyframe names none, so it moves at a constant speed (linear). Its transition's `direction` makes the slide leave with a push upward.

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
A curve with settings is a record with the curve's name in `type`. `back` sets how far an overshoot swings past the end, and `bounciness` sets how high a bounce rebounds. `amplitude` and `period` set an elastic curve's swing, and `amount` and `response` set a spring's.

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

<!-- text: label-path -->
A curved path

<!-- text: caption -->
The card loops, the dots bob, the oval zooms evenly, a dot turns back halfway and the last one arcs over.

<!-- notes -->
The card slides in past its resting position, settles, and then swings right and back, looping until the three-second step ends. Its curve's `loop` replays the keyframes from its `loop-keyframe`, `card-settles`, to the last one, so the slide-in plays once. The two dots bob on waves from `oscillate`. One is a triangle wave that starts a quarter of the way into its cycle, and the other is a noise wave from seed 7. The oval starts at a `scale` of 0.5 by 0.25 and grows fourfold. `exponential-scale` keeps the zoom even. The fourth dot heads for the far side, but its keyframe cuts that move off halfway, and the dot turns back. The keyframe's `interrupted` names the move it cuts off. The last dot arcs over to the right. Its curve animates `frame.xy`, both coordinates as one point. Its keyframe's `path` bends the move along a curve drawn in the move's own frame, from the start at 0 0 to the end at 1 0. Its transition's `color` makes the slide leave through the accent color.

<!-- slide: words -->

<!-- text: heading -->
Words in pieces==.==

<!-- text: body -->
These letters arrive six at a time, in a shuffled order.

This paragraph waits a moment before its turn.

<!-- text: caption -->
These letters start with their exit already played.

The second click plays it backward.

<!-- notes -->
A wipe reveals the bar as its `progress` goes from 0 to 1. The body's letters enter six at a time (`spread`) in a random `order` from `seed` 7. They slide in from the left (`from`), and each space takes a turn too (`spaces`). The body's paragraphs take turns, each in its own shuffled order, with a `pause` between them. The body's `progress` starts at 0, and the first click runs it to 1. The caption starts with its exit at a `progress` of 1, so it's gone. The second click runs the exit back to 0, so the caption's letters turn and sharpen into place.

<!-- slide: copies -->

<!-- text: heading -->
Copies of one shape==.==

<!-- text: caption -->
Eight copies turn in a ring. Each starts a little later than the one before.

<!-- notes -->
With `repeater`, one square makes eight squares in all (`copies`): itself, unchanged, and seven copies. Each copy is turned 45 degrees (`rotation`) around a point below it (`anchor-point`) and is a little smaller (`scale`). The last copy fades to an `end-opacity` of 0.2. Each copy's turn, scale and delay vary a little at random (`rotation-randomness`, `scale-randomness`, `stagger-randomness`), from `seed` 5. The click turns the square, and each copy follows 0.08 seconds after the one before (`stagger`). The slide leaves with a carry-over that matches words, so the words shared by the two headings move to their places on the next slide.

<!-- slide: arrival -->

<!-- text: heading -->
Eight copies of one shape==.==

<!-- text: caption -->
The words shared by both headings carry over from the slide before.

<!-- notes -->
The previous slide's transition has its `effect` set to `carry-over` and its `match` set to `word`, so it matches the two headings word by word. "of one shape" moves to its place in this heading, "Copies" fades out and "Eight copies" fades in. The square has the same id on both slides and is where the click left it, turned 90 degrees, so its copies stay still while the words move.

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
The sweep composition moves its dot along the track in 2 seconds. Each instance's `time-remap` sets which moment of that move it shows. The fourth instance has no `time-remap`, so it shows the last moment. A step can animate `time-remap` to play the composition at any speed.

<!-- composition: sweep -->
