---
markdownpoint: "1.0"
id: b3f8ddb9-7a67-467a-95be-9bd78347d8fa
---

<!-- slide: constraints -->

<!-- text: heading -->
Constraints==.==

<!-- notes -->
The lamp's shade turns toward the mug with an `aim` constraint on its shade `node`. The mug slides in and stretches along x on the way. Its `squash-and-stretch` stretches it along its `free-axis`, x, and pulls it toward 0.9 of its `volume` at 0.9 `strength`, so it thins as it stretches. Its rotation is written as a quaternion (`rotation-mode`). The camera follows the mug a quarter of the way along x, by a `position` constraint on the `axes` x at `strength` 0.25, with `relative-to-target` false. Its `motion-blur` shows only in movies and the Agent's rendered frames.
