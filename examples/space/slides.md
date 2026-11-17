---
markdownpoint: "1.0"
id: b3f8ddb9-7a67-467a-95be-9bd78347d8fa
---

<!-- slide: constraints -->

<!-- text: heading -->
Constraints==.==

<!-- notes -->
The lamp's shade turns toward the mug with an `aim` constraint on the lamp's
shade `node`. The mug slides in and stops. A mug is rigid and keeps its shape.
As it stops, it tips toward its travel and rocks back upright. It turns about
its base's center. Its `position.y` rises with each tip and keeps the edge it
tips onto on the floor. Its rotation is written as a quaternion
(`rotation-mode`), and each `rotation` keyframe holds a whole turn. Then a rubber ball drops beside the mug and bounces to rest. Its
`scale.y` keyframes stretch it along each fall and squash it at each contact.
Its `squash-and-stretch` constraint scales x and z to keep its volume. The
camera follows the mug a quarter of the way along x. It uses a `position`
constraint with `axes` set to `["x"]`, a `strength` of 0.25, and
`relative-to-target` set to false. The camera's `motion-blur` shows only in
movies and in the Agent's rendered frames.
