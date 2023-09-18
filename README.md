<a name="br1"></a> 

CSE 535 Project 2: Context-Aware Adaptation

In Project 1 you learnt about developing a monitoring application using on-board sensors in a smart

phone. In this project, we will design a context aware adaptation strategy for the development of

autonomous braking system for Level 3 autonomous cars which assumes an underlying distributed

monitoring and actuation cyber-physical system specifically human-in-the-loop and human-in-the

plant. Before we delve into the details let us understand some basic definitions.

Level 3 autonomy: The vehicle operates autonomously, however, requires the driver to be

attentive at all times for potential switch to manual mode.

Controller: It is a software that takes sensor data from the vehicle such as speed, distance

between cars and acceleration or deceleration and outputs the braking pressure to apply

deceleration to stop the car.

Controller gain: It is a property of the controller that determines how aggressively a

controller will apply brakes and stop the car.

Deceleration Limit: This is the maximum limit on the deceleration applied by the controller.

High deceleration can lead to unwanted harm to the driver behind the wheel. The controller

can never apply deceleration that is higher than the maximum limit.

Controller frequency: It is the number of times the controller needs to compute a new

braking input per second.

Reaction time: It is the time taken for the human to decide on an action after a switch to

manual mode

Action time: Time taken to execute the action decided by the human and stop the car.

Autonomous vehicle braking system example

Figure 1: Autonomous Driving example

Consider a user driving a Level 3 autonomous car (Fig. 1). We are specifically focusing on the

autonomous braking system. As a part of the autonomous braking system, there is an advisory

control that:



<a name="br2"></a> 

a) processes a driving scenario, by sensing environmental conditions

b) Predicts if the autonomous braking system will be able to stop the car

c) Determines if the control should be transferred to the human behind the wheel

Autonomous braking system has a feed-back loop controller that senses the distance between the

autonomous car ꢀ and the car in front ꢁ, and computes a braking force as a function of the ꢀ’s initial

speed ꢂ<sub>ꢀ</sub>, and distance between ꢀ and ꢁ. The braking force applied is also a function of the

controller gain ꢃ, which is a design parameter that you will have to tune.

The braking force is constrained by the maximum deceleration limit ꢄ<sub>ꢁꢂꢃ</sub>, which is dependent on

the road condition. For example, on dry road you can apply more deceleration than on wet road in

rainy season. The braking force is thus given by the following equation:

ꢅꢆꢄꢇꢈꢉꢊꢋꢌꢍꢆꢎꢏꢋꢐ = minꢋ(ꢌ(ꢂ , ꢑ , ꢃ), ꢄ<sub>ꢁꢂꢃ</sub>).

ꢀ

ꢀꢄ

Once the braking force ꢐ is applied, the vehicle kinematics show how the vehicle behaves in the real

world. Given the initial speed ꢂ and initial distance between ꢀ and ꢁ, ꢑ , the vehicle kinematics

ꢀ

ꢀꢄ

gives the stopping distance ꢑ<sub>ꢅꢆꢇꢈ</sub>, and stopping time ꢒ<sub>ꢅꢆꢇꢈ</sub> for the vehicle. This you can obtain using

the vehicle kinematics Simulink model (ꢓ<sub>ꢉꢊꢋꢌꢍꢎꢊ</sub>) provided to you.

If ꢑ<sub>ꢅꢆꢇꢈ</sub> ≥ ꢑ<sub>ꢀꢄ</sub> then the autonomous vehicle fails, and the cars collide, else there is no collision. If

cars collide, then stopping time ꢒ<sub>ꢅꢆꢇꢈ</sub> is same as collision time ꢒ<sub>ꢍ</sub>.

In this example, we will fix the distance between cars ꢀ and ꢁ, ꢑ<sub>ꢀꢄ</sub>. However, changing initial velocity

ꢂ<sub>ꢀ</sub>, controller gain ꢃ, and deceleration limit ꢄ<sub>ꢁꢂꢃ</sub>, can change the stopping distance and stopping

time resulting in potential collisions.

Human driver model

The human driver is assumed to be alert all the time to take over control. However, we have to

account for the reaction time ꢒ<sub>ꢏ</sub> of the human driver after a switching signal is sent through the

dashboard controls. In addition to the reaction time the human action also takes some time to

execute.

In this example, we assume that the human provides a deceleration that is 10% higher than the

deceleration limit ꢄ<sub>ꢁꢂꢃ</sub>, i.e. ℎ<sub>ꢂ</sub> = 1.1ꢄ<sub>ꢁꢂꢃ</sub>. The action time ꢒ<sub>ꢂ</sub> can be obtained by removing the

braking controller from the Simulink model and applying a static deceleration of ℎ<sub>ꢂ</sub> into the vehicle

kinematics model. After simulation the action time can be obtained as the stopping time reported in

the Simulink model.

The total time taken by the human to completely stop the car is ℎ<sub>ꢅꢆꢇꢈ</sub> = ꢒ + ꢒ .

ꢏ

ꢂ

We are now in a position to define a basic logic for the advisory control. The advisory control uses

the following algorithm to decide if it wants to switch:

SWITCH = ADVISORY CONTROL (ꢂ<sub>ꢀ</sub>,ꢃ,ꢄ<sub>ꢁꢂꢃ</sub>, ꢒ<sub>ꢏ</sub>)

Step 1: Predict if autonomous braking control collides or not using ꢂ<sub>ꢀ</sub>, ꢃ, ꢄꢉꢑꢋꢄ<sub>ꢁꢂꢃ</sub>

.

Step 2: If no collision then

Step 3:

Do not switch to manual control



<a name="br3"></a> 

Step 4: Else

Step 5:

Collision time ꢒ<sub>ꢍ</sub>= predicted stopping time from Step 1

Step 6:

Predict action time ꢒ , using ꢂ , remove controller, and constant

ꢂ

ꢀ

deceleration 1.1ꢄ<sub>ꢁꢂꢃ</sub>

.

Step 7:

Step 8:

Step 9:

Step 10:

Step 11:

Step 12:

Predict reaction time ꢒ<sub>ꢏ</sub>

Compute ℎ<sub>ꢅꢆꢇꢈ</sub> = ꢒ<sub>ꢏ</sub> + ꢒ<sub>ꢂ</sub>

if ꢔ<sub>ꢐꢑꢒꢓ</sub> < ꢕ<sub>ꢔ</sub>

Switch to human

Else

Do not switch.

Driving Context Changes

Road conditions depend on several factors including:

a) User mobility patterns

b) Traffic conditions

c) Weather patterns

Each context can be modeled using stochastic modeling techniques such as Markov chains. In this

assignment you are given a composite model of driving context that has two states: a) normal with

ꢁ

ꢅꢊꢍ<sup>!</sup>

ꢁ

ꢕꢖꢗ<sup>!</sup>

deceleration limit -200

and b) poor road conditions with deceleration limit -150

.

The prior probabilities of each state ꢖ(ꢉꢍꢆꢗꢄꢘ) = 0.6, ꢄꢉꢑꢋꢖ(ꢖꢍꢍꢆ) = 0.4. The transition

probabilities are given by the matrix:

ꢚꢍꢆꢗꢄꢘ ꢛꢍꢍꢆ

ꢙ = ꢋ ꢚꢍꢆꢗꢄꢘ

ꢛꢍꢍꢆ

0\.6

0\.4

0\.15

0\.85

Human Reaction time model

The cognitive workload of a human changes based on driving context. We will assume that normal

driving conditions have low workload, and poor driving conditions require high workload.

Consider Table 1 i[n](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6338053/pdf/fnhum-12-00525.pdf)[ ](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6338053/pdf/fnhum-12-00525.pdf)[https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6338053/pdf/fnhum-12-](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6338053/pdf/fnhum-12-00525.pdf)

[00525.pdf](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC6338053/pdf/fnhum-12-00525.pdf)

The heart rate and respiratory rate is provided for single task under low cognitive workload (LCW)

and high cognitive workload (HCW) tasks. Use the mean and standard deviation to create a gaussian

model of heart rate and respiratory rate. For each driving condition, the heart rateꢋꢜꢆ and



<a name="br4"></a> 

respiratory rate ꢝꢆ is sampled from the gaussian model. The respiratory quotient can be computed

as ꢝꢙ = <sup>ꢘ</sup><sub>ꢙꢏ</sub><sup>ꢏ</sup>. The human reaction time ꢒ<sub>ꢏ</sub> can be computed using the following equation:

ꢒ<sub>ꢏ</sub> = 0.2 ∗ ꢝꢙ

Project Tasks

The project has three specific tasks as shown in Fig 2.

Fig 2: Project tasks

In this project, we will first attempt to design the advisory control for an average user. Then choose

a specific user and modify the advisory control design at deployment. Finally, after deployment

revert back to the design phase to change the advisory control and personalize it for the specific

user.

This mimics the standard context aware application development workflow. Here the manufacturer

initially designs a system to be used for a large set of users. After deployment, a specific user can

make some limited configuration changes to the system. The data is collected during deployment.

This data is then used to redesign the system to personalize for specific user profiles.

Task 1: Controller design

Given:

1\. A set of initial speeds, 20 kmph to 40 kmph

2\. Deceleration limits of normal (-200 ꢞ/ꢟ<sup>ꢚ</sup>) and poor (-150 ꢞ/ꢟ<sup>ꢚ</sup>)

3\. User profiles

User

LCW

HCW

HR

RR

HR

RR

1

80 (±ꢠꢡ)

16 (±6)

95 (±ꢢꢣ)

21 (±ꢠꢡ)



<a name="br5"></a> 

2

3

65 (±ꢠꢤ)

61 (±ꢠꢡ)

13 (±ꢡ)

17 (±ꢥ)

71 (±ꢢꢠ)

92 (±ꢢꢦ)

14 (±5)

26 (±ꢠꢣ)

To do:

1: Create an average user profile

2\. Determine reaction time model

3\. Determine controller gain for each road condition such that collision is minimized for the

given initial speed range

4\. Determine the number of switches necessary to avoid collision for your design

Task 2: Reaction time update in advisory control

Given:

1\. User 3 as the actual user of the system

2\. Initial speed range 20 kmph to 60 kmph

3\. Fix the gains for Normal and Poor road condition from Task 1.

4\. Markov chain model for road condition generation

To do:

1\. Using the monte Carlo Markov chain simulation method generate a sequence of length

100 of normal and poor road conditions

2\. For each road condition in the sequence, sample the initial speed range uniformly to

get one initial speed

3\. For each road condition in the sequence, sample the HR and RR models of User 3 to

obtain one HR, one RR and consequently one reaction time for the user.

4\. Evaluate collision status for each road condition in the sequence.

5\. Evaluate the number of switches in each road condition in the sequence.

6\. Determine the reaction time setting in the advisory control that gives the least number

of collisions with the least number of switches.

Note here that the reaction time of the human user is independent of the reaction time setting

of the controller.

So there will be scenarios where even if you decide to switch according to the current reaction

time of the human driver it is not feasible for the user to stop the car and as a result there is a

collision.

Task 3: Gain personalization

Given:

1\. User 3 as the actual user of the system

2\. Fixed reaction time setting of advisory control

3\. Initial speed range of 20 to 60 kmph



<a name="br6"></a> 

To do:

Update the gains for each road condition such that the number of collisions and number of

switches are both reduced.


