# CMSC_240_Final_Project
Brett Barnes and Jack DuPuy Final Project README

Design Decisions: 
- Our main design philosophy was segmented components. The reasoning for this was twofold: first, it allowed us to work on different components simultaneously and fit them together upon completion, and second it allowed us to be able to test and edit different sections of the project in an isolated environment. It also was much cleaner and made it easier to conceptualize various sections of the whole, as opposed to trying to focus on the entire big picture. The principal example of this is how we rely on a series of methods to handle the loading and movements of our vehicles; each of these passes a vector by reference and can thus operate on any of the directions. This was much more elegant than doing a separate, hard-coded movement for each direction. The generate() method, which figures out if vehicles should be created, and if so, what type they should be, is not reflective of this, but this is because it is called once per loop and tackles all four directions at once. 
- Our second-most critical design decision was to construct the multi-sectioned vehicles as a series of equivalent but independent pointers, which do not inherently know they are part of a larger vehicle. This has several advantages, specifically allowing for ease of movement and changing between direction vectors, but also some drawbacks, primarily when calculating the logic for beating the red light and for making left turns around oncoming traffic. 
- Another (albeit more minor) design choice we made in order to simplify the code was to combine the green and yellow light counters into one variable, as it makes no difference to the vehicles or the intersection logic whether their light is green or yellow.
- One other design choice we made was in how we read input. By getting a line (if it’s not blank) and then removing all white space, we could control for lines where the colon was touching zero, one, or both of the key and value for each variable. Furthermore, we added these lines to a dictionary (map) so that the order of key-value pairs in the input file does not matter.
- For left turns, a design choice we made was to give northbound and eastbound vectors priority if there are vehicles simultaneously turning left in opposing vectors.

Compile & Run Instructions: 
For our Simulation.cpp
1) make
2) ./Simulation [input file] [initial seed]

For Safety_Submission.cpp
1) g++ -Wall Animator.cpp VehicleBase.cpp Safety_Submission.cpp -o Safety
2) ./Safety [input file] [initial seed]
(Feel free to use and edit our sample_input.txt file included in the tar ball or create your own input file with different parameters)

Tests Ran: 
Our primary testing was comprised of testing different input parameters with multiple seeds:
- Probability of a vehicle generating in a given direction ranging from 0-1
- Probabilities of each vehicle ranging from 0-1
- Probabilities of each turn ranging from 0-1 (including 0 for all left turns, for which our Simulation.cpp worked as intended, making safety submission possibly redundant)
- Number of sections before intersection ranging from 1-15
- Varying lengths of lights ranging from 1-15 (for both green and yellow)

We ran each simulation for several hundred ticks to ensure there were no problems with cars separating, crashing, running red lights, disappearing, skipping sections, moving backwards, getting frozen, or turning the wrong way. We were careful to observe a wide variety of edge cases with respect to vehicles entering the intersection at different times. In terms of vehicles going straight and turning right, we confirmed that any time a vehicle could move, it would. Unfortunately, we were close but not perfect with left turns (discussed below). Outside of the simulation itself, we successfully tested the copy and move semantics of our VehicleBase class as well as the ability of our readInput() function to read input from files containing varying white space, blank lines, and orders of input parameters.

Important Points and Errors Uncovered:
- We included a Safety_Submission.cpp file that has no left turn logic at all. Our Simulation.cpp handled the case of no left turns as intended, so the safety file may be redundant; we included it anyway just to be safe.
- There are 3 warnings generated when compiling with -Wall that result from us adding enumerated types to a couple of the enumerated classes (Direction, VehicleType, LightColor) and creating our new enumerated class Turn within VehicleBase.h. For whatever reason, Animator.cpp has an issue with those additional types, but our code successfully compiles and runs despite the warnings. Otherwise we have no warnings or compiler errors.
- Based on our testing, we believe that we have no errors with our vehicles that go straight and turn right. They go whenever they can, stop only when they can’t make it through the intersection or there’s a car in front of them, follow the light logic correctly, and never break in half or crash into each other.
- Based on our testing, we are aware of the following errors with our left turns: 
1) all left turning vehicles wait too long before beginning their turn. From the point of view of the left turning vehicle, the front of said left turning vehicle should be in the top left corner of the intersection when the oncoming vehicle going straight is in the bottom left corner of the intersection. Unfortunately, our left turning vehicles are two ticks behind that and similarly lag behind when the oncoming car is turning right.
2) the other issue we have is that sometimes vehicles do not turn left even if they have time to make it through the intersection and the oncoming car does not (and therefore must stop). For example, if a left-turning car and an oncoming straight-going truck arrive at the same time with 6 ticks left, the truck must stop and the car should go, but our car does not take any chances and waits until the next light cycle to go. 
- While these errors are unfortunate, our attempts to fix them led to far more catastrophic errors such as traffic deadlocks, the splitting or merging of vehicles, and/or vehicle accidents in the intersection. While our left turns may not be perfect, we are proud of the fact that everyone consistently makes it through the intersection alive and well and in a relatively timely manner.
