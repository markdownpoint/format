---
markdownpoint: "1.0"
id: b3f8ddb9-7a67-467a-95be-9bd78347d8fa
---

<!-- slide: constraints -->

<!-- text: heading -->
Constraints==.==

<!-- notes -->
The lamp's shade turns toward the mug with an `aim` constraint on the lamp's shade `node`. The mug slides in and stretches along x on the way. Its `squash-and-stretch` stretches it along its `free-axis`, x, and pulls it toward 0.9 of its `volume` with a `strength` of 0.9, so it gets thinner as it stretches. Its rotation is written as a quaternion (`rotation-mode`). The camera follows the mug a quarter of the way along x. It uses a `position` constraint with `axes` set to x, a `strength` of 0.25 and `relative-to-target` set to false. The camera's `motion-blur` shows only in movies and in the Agent's rendered frames.
