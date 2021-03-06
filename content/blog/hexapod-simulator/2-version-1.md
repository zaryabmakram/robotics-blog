---
title: "🕷️ Hexapod Simulator: Version 1.1"
date: 2020-04-08T16:33:32+08:00
categories: [hexapod-simulator, hexapod-robot-simulator]
tags: [hexapod-robot-simulator, hexapod-simulator, hexapod]
draft: False
---

My objective to build a hexapod robot simulator from first principles. I'll be using solely homogenous transform matrix, and not quaternions. I'm aware that quaternions is inarguable the better solution if not the best, not only the elegant math, but the speed of calculation. My reason is that I'm not that familiar with quaternions. In the future, when I have time I'd study quaternions and reimplement the inverse kinematics.

You can currently check it out on [on heroku](https://hexapod-robot-simulator.herokuapp.com) but I highly suggest that
you run it on your own local server. When this application is run locally it's really snappy! On the other hand, the link above is barely usable. Here's the [repository containing the source code](https://github.com/mithi/hexapod-robot-simulator)

### Bare minimum

1. I am able to install and run it on my laptop, or view it on the web.
2. I could see the hexapod in a 3D plot.
3. I could specify the hexapod dimensions
4. Given the roll, pitch, yaw, translation of the body, foot positions, the corresponding 18 joint angles would be displayed.

I wanted to build something that, you know, just works.
I decided it should be web browser-based. This is a hard and fast rule. It should have minimal dependencies, everything should be made simple, but not simpler.

|Twisting turning and tilting| Leg pattern movements | Customizing Hexapod Dimensions | Adjusting camera view |
|---------|---------|---------|---------|
|![Twisting turning and tilting](/robotics-blog/robot-only-x1.gif)|![Leg pattern movements](/robotics-blog/robot-only-x2.gif)|![Customizing Hexapod Dimensions](/robotics-blog/robot-only-x3.gif)|![Adjusting camera view](/robotics-blog/robot-only-x4.gif)|

### What technology to use? Hmm

I needed a 3D graphing library that had a very low-learning curve.

I didn't know anything about Javascript, is this finally the time I get to learn it? What is the easiest 3D plotting library that I could use? [ThreeJs](https://threejs.org/) has a lot of features but it has a relatively steep learning curve. So is [D3js](https://d3js.org/). It would take me forever to implement it in [WebGL](https://github.com/KhronosGroup/WebGL).

I settled with using [Plotly-Dash](https://plotly.com/dash/) so I can quickly prototype it and not get bogged down by the details of plotting. I already know Python very well. And worst case, I could always use a different library in the future. The important thing is to be able to code the kinematics and inverse kinematics and have a visual feedback for every step of development.

## My progress so far

Here's a preview of how it works

| ![Inverse Kinematics](/robotics-blog/UI-1.gif) |
|----|
| ![Kinematics](/robotics-blog/UI-2.gif) |

### Features

| Feature   | Description  |
|-----------|--------------|
| 💕Forward Kinematics | Given angles of each joint, what does the robot look like?|
| 💕Inverse Kinematics | What are the angles of each joint to make the robot look the way I want? Is it even possible? Why or why not? |
| 💕Uniform Movements | If all of the legs behaved the same way, what will the robot look like? |
| 💕Customizability | Set the dimensions and shape of the robot's body and legs. (6 parameters) |
| 💕Usability | Control the camera view, pan, tilt, zoom, whatever. |
| 💕Simplicity | Minimal dependencies. Only depends on Numpy for calculations. Plotly-dash for plotting, Plotly can be safely replaced if a better 3d plotting library is available |
| ❗ Stability Check (WIP) | If we pose the robot in a particular way, will it fall over? |
| ❗Fast | Okay, it's not as fast as I wanted, but on a local server, it's okay |
| ❗Bug-free | Okay, right now there's still room for improvement |
| ❗Well-tested | Yeah, I need to build compile test cases first |

### More screenshots

| Screenshots |
|----|
| ![Inverse Kinematics](/robotics-blog/screenshot-1.png) |
| ![Kinematics](/robotics-blog/screenshot-2.png) |
| ![Leg patterns](/robotics-blog/screenshot-3.png) |
| ![Alternative Inverse Kinematics](/robotics-blog/screenshot-4.png) |

## Priorities as of the moment

- Check pose stability. The inverse kinematics solver doesn't yet actually check if the center of gravity is inside its support polygon.
- Compile integration and unit tests cases
- Reimplement inverse kinematics solver (**`hexapod.ik_solver2`**). The current implementation [`hexapod.ik_solver`](https://github.com/mithi/hexapod-robot-simulator/blob/master/hexapod/ik_solver.py) involves a bunch of tiny helper methods and one big god function that tries to do almost everything. I plan to redesign this by making a class that centers around this responsibility.
- Improve the code quality of `hexapod.models` and `hexapod.linkage` and  `.hexapod.ground_contact_solver` modules

## Known issues as of the moment

- ❗ Some unstable poses are not marked as unstable by the hexapod which might be a bug somewhere in [`hexapod.ground_contact_solver.three_ids_of_ground_contacts`](https://github.com/mithi/hexapod-robot-simulator/blob/e19f5de5b1110bc78bd75091eb63f47907ffddc5/hexapod/ground_contact_solver.py#L45) or [`hexapod.models.VirtualHexapod.update`](https://github.com/mithi/hexapod-robot-simulator/blob/e19f5de5b1110bc78bd75091eb63f47907ffddc5/hexapod/models.py#L141)
- ❗ When the right-middle leg is twisted by itself (coxia angle changed), the figure point-of-view changes, other legs don't do this. Might be a bug in [`hexapod.models.VirtualHexapod._find_if_might_twist`](https://github.com/mithi/hexapod-robot-simulator/blob/e19f5de5b1110bc78bd75091eb63f47907ffddc5/hexapod/models.py#L192) or [`hexapod.models.find_twist_frame`](https://github.com/mithi/hexapod-robot-simulator/blob/e19f5de5b1110bc78bd75091eb63f47907ffddc5/hexapod/models.py#L231)
- ❗ Leg can criss-cross each other which shouldn't be the case, I'm open to hearing ideas on how to go about this
- [Other issues](https://github.com/mithi/hexapod-robot-simulator/issues)
