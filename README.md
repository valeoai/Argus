<div align="center">

# Argus: From Rasterized-Camera Self-Play to Real-Camera Driving

[Yuan Yin](https://yuan-yin.github.io/)<sup>1,\*</sup>, [Elias Ramzi](https://elias-ramzi.github.io/)<sup>1,\*</sup>, [Tuan-Hung Vu](https://tuanhungvu.github.io/)<sup>1</sup>, [Marc Lafon](https://marclafon.github.io/)<sup>1</sup>, [Yihong Xu](https://www.linkedin.com/in/yihong-xu-326285109/)<sup>1</sup>,<br>
[Éloi Zablocki](https://eloiz.github.io/)<sup>1</sup>, [Alexandre Boulch](https://www.boulch.eu/)<sup>1</sup>, [Andrei Bursuc](https://abursuc.github.io/)<sup>1</sup>, [Matthieu Cord](https://cord.isir.upmc.fr/)<sup>1,2</sup>

<sup>1</sup>valeo.ai, Paris, France · <sup>2</sup>Sorbonne Université, CNRS, ISIR, Paris, France · <sup>\*</sup>Equal contribution

*Driving learned by reinforcement learning in simulation, carried to real cameras.*

**[Project page](https://valeoai.github.io/Argus) · [Videos](https://valeoai.github.io/Argus#videos)**

<img src="https://valeoai.github.io/Argus/assets/img/teaser.png?v=2" width="96%" alt="Argus: the same right turn planned by Argus-Raster on a raster twin and by Argus on the real camera frame, AlpaSim PAI score against hours of camera data, and closed-loop scores of Argus+ against the best camera planners on four benchmarks.">

</div>

## Abstract

Self-play reinforcement learning (RL) in simulation lets driving scale with compute rather than with logged human data, but learning from scratch takes billions of steps, so observations must be cheap to render. Rasterized camera views show what driving mostly depends on, the road, lanes, road users, and traffic lights, at a fraction of the cost of realistic images, and through a pretrained image encoder such as DINOv3, a real camera image resembles its raster.

We present **Argus**, a camera policy that inherits all of its driving skills from *Argus-Raster*, a raster policy trained by self-play, interleaved toward the end of its training with single-agent PPO in logged scenes. Only its pretrained image encoder is aligned: it learns to match *Argus-Raster*'s features on a raster twin of the scene, drawn from the perception labels that driving datasets provide. Its driving comes from 5M hours of RL in simulation, so Argus needs little camera data. Aligned on only 2.2 hours of reconstructed camera views of driving scenes, it already beats the best camera planner on AlpaSim PAI, a model trained on 1,800 hours of driving. Aligned on 25 hours of such views, Argus outperforms in closed loop on AlpaSim and HUGSIM every camera policy we compare with, learned by imitation or transferred from self-play, by 5.8 points on PAI and 4.9 on nuPlan, and *Argus+*, with a larger image backbone, is ahead of every planner it is compared with on AlpaSim, HUGSIM, NavSafe-∞, and Odyssey, with 87.3 on PAI.

## How Argus works

<div align="center">
<img src="https://valeoai.github.io/Argus/assets/img/overview.png?v=2" width="96%" alt="Overview. Step 1: Argus-Raster learns to drive by self-play RL in simulation; a raster encoder E and a driving head H turn rendered raster views into actions, a value and a trajectory. Step 2: Argus replaces the encoder with DINOv3 + LoRA (B) and an adapter (A) that read real camera views; during alignment only, they learn to match E's scene summary on a raster twin of the scene and the actions and value of the frozen head H.">
</div>

**1 · Argus-Raster.** One policy drives every vehicle, cyclist and pedestrian from rendered raster views. A raster encoder *E* summarizes the views, and a driving head *H*, shared across camera rigs, outputs actions, a value and a trajectory. It learns by self-play RL from rewards over 5M hours of driving, then extended to logged traffic.

**2 · Argus.** Each real camera view gets a raster twin, read by the frozen Argus-Raster. Only the encoder is replaced: *B* (DINOv3 + LoRA) and an adapter *A* learn to match the scene summary, then the actions and value. The result drives from real cameras, with all of its driving learned in simulation.

The [project page](https://valeoai.github.io/Argus) plays this figure as a step-by-step explainer.

## Driving examples

<div align="center">
<a href="https://valeoai.github.io/Argus#videos"><img src="https://valeoai.github.io/Argus/assets/img/thumbs/normal_1_poster.jpg?v=2" width="60%" alt="A closed-loop drive of Argus through a roundabout: the front camera on top, the four side cameras below, and the trajectory Argus plans drawn on the road."></a>
</div>

Closed-loop drives of Argus in AlpaSim, on PAI validation scenes reconstructed with NuRec, are on the [project page](https://valeoai.github.io/Argus#videos): each clip shows the five camera views the policy receives and the trajectory it plans from them, with passes and failures.
