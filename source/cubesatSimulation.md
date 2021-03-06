title=CubeSat Simulator
description=Your Computer is low on memory - ERRCODE 98
date=2/2022
category=Gifted
status=Finished
%

# CubeSat Simulator
### I am not kidding when I say you need 16GB of memory 

![bluescreen of death](https://upload.wikimedia.org/wikipedia/commons/5/56/Bsodwindows10.png)

*Link to the paper is at the bottom*

If you haven't gotten the idea yet, I really like space. It happens that I really like computers. Building on this, I wrote a simulation for my last project so I decided that it would be a fine project to actually research the maths/methods that people employ to actually perform these simulations.

I incorporated this desire into a project that fits into the Gifted Independent Studies program by making it a sort of competition. I would simulate a CubeSat while simulating various control systems and by comparing their metrics, decide on a objective winner. I hate the concept of subjective favorites in engineering and the fact that for a gifted project we must have a argumentative research question irks me.

Anyways.

I outlined a seemingly trivial goal:
Simulate the all the physics of a CubeSatellite orbiting the earth while also simulating a onboard control system that is constantly working to maintain a preset attitude (point to the earth).

Very simply, the program creates a CubeSat and using the Kepler equations it follows precalculated path at a precalculated velocity. Using its current physical state the program can then feed a three axis PID loop with all the data it needs to perform a correction. For the reason I stated above, the program logs every-bit of data it can, and I mean that in the most literal way.

## Optimization Hell

A criticism I received on this rather short paper is that I kept complaining about how inefficient this program is, and so I am going to fix that here.

C++  (the programming language I used) is a strictly typed language so every variable/object must have a predefined type. This lets the compiler know whats going on and helps the code be more readable as you have guaranteed attributes on a object. In C++ there is not a "number" class, instead there are three different standard ways to store numbers:
- a Integer
- a Floating-point number
- a Double

A integer consumes the least amount of memory and can only hold whole numbers, so we can dismiss that as the scalar type we will use in the project.

So it boils down to mostly floats vs doubles, which are both data-types that hold real numbers. The major difference between them is that doubles, as the name implies, have twice as much memory allocated to them then floats. Generally floats are 32 bit while doubles are 64 bits. What this means is that doubles have the capacity to hold significantly more data then a float can.

The maximum value a float can hold is `3.402823466 E + 38` while a double can hold up to `1.7976931348623158 E + 308`. As we are in the realm of space and we are gunning for precision, doubles become the defacto choice. If we used floats, the data would probably be fine, but the error would slowly acculumate as the CPU automatically rounds are interpolates in a effort to maintain a semi-accurate number. For more information on the subject, google "floating point precision" it is a issue especially prevelant in video games. There is a downside to using doubles of course, and the cause you can probably guess: they use twice as much memory as floats. This means they consume more memory, yes, but also it puts more of a burden on the CPU (*note: this is a massive simplification*). Additionally, (this is not as true for modern CPUs) most CPUs are optimized for floating point math as it is historically the most common datatype used and they are equipped with FPUs, floating-point units.

Anyway, this does slow down the execution on the program, and when combined with a very small timestep (dt) in the simulation, it can take hundreds of thousands of iterations before the simulation concludes.

Also, all this data is saved in memory before being written to a file, and this is the crux of the problem. Computers love numbers, and by their vary nature, are slow are performing string operations like manipulating text, etc.
Formatting hundreds of thousands of lines of text and then writing that to a file is incredibly slow and was the main bottleneck in the code. It's a long story about optimizers and compile-time issues but the actual simulation only takes 2 seconds to run for several orbits at a dt of 0.001 while the actual logging of the data takes > 5 minutes. Additionally the size of the logged files are gigabytes big which is also a issue.

I made the classic mistake of listening to the allure of precision and ignoring the all the red flags on the way.

## Physics Teachers


![phyusics](https://www.researchgate.net/profile/Chengliang-Huang/publication/224141414/figure/fig1/AS:393761701023751@1470891513830/Global-coordinate-system-and-the-local-coordinate-system.png)

*Source: Chengliang Huang*

During this project I encountered a pretty important problem I needed to fix. It was seemingly trivial to do, but I did not how to peruse it. Essentially, I have a box with a rotation, lets say its rotated by 90 degrees on its y-axis. This means if I just naively add forces, it fails to account for the rotation of the body, meaning that it is not relevant to the local space of the box. It is a simple problem, with a not simple solution. I therefore asked the physics teachers, which mostly resulted in some quizzical looks and a brief explanation on the law of cosines which was semi-helpful. 2 teachers later, I kinda gave up and just tried to find a solution myself. Turns out the solution was literally already in my code, I just did not realize it. Briefly, I used Quaternions (if you want to know what these are, I recommend Jackson's project) as the representation of rotation and I forgot a crucial property of them: you can multiply them by vector in order to rotate it. Therefore, I can supply a force vector, rotate it by the current rotation, and bam, it is in local space.

## Math is hard
A major issue with this project is me. This project pushed me to the limit of my mathematical capabilities (essentially I do not know calculus) and that was a issue. In terms of practicality, I spent more time research the maths then doing the actual assignment, and even then remained pretty confused. It makes me mad when I write code that I do not fully understand, and that is a good summary of this entire project to be honest.

*Link*:
<https://docs.google.com/document/d/1EzB3HeAWPFEnI7cNzzeX8pwnApASo7rb_loVFkTwNqY/edit?usp=sharing>