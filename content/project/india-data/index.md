---
date: "2020-05-01T00:00:00Z"
external_link: ""
image:
  caption: Logic Flowchart
  focal_point: Smart

links:
- name: Download PDF
  url: Robotic_Systems.pdf
summary: A system architechture repository for functioning, path planning & odometry. 
tags:
- Robotic systems, Odometry
title: Romi (The line following Robot)
---

The flow chart provided in Figure 1 acts as a visual representation of the decision procedure which was designed and then implemented in code, allowing the Romi to complete the line following task. The procedure of the system can be easily represented by the various ordered states of operation that the Romi can be in (State 1 through State 4). This is an example of a Finite State Machine (FSM) and it's implementation will be explained further towards the end of this section. On boot the Romi starts up and proceeds into State 1. This results in the Romi undergoing a reset procedure, displaying "RESET" to the user in the Serial monitor and initialises variables. After completion the Romi the begins the Calibration procedure. This is important as it allows for a baseline background colour (IR intensity value) to be obtained which can be used to subtract from the read in raw value of the Romi report 1 sensors. The hope of this is that the white background of the track will now be re scaled such that it reads an approximate value of 0 across the whole background of the track and can act as the baseline from which calculations relating to the line can be made. 

Additionally, the calibration procedure was completed whilst the Romi was moving forward and was calculated by sampling the IR intensity 50 times during this period and taking the average value of the result. By completing the calibration procedure in this manner the calibration value obtained would be more representative of the white background workspace and was found to be more reliable than a single calibration value, which would be more likely be eected by changes in light and would have a greater chance of creating a systematic error which would impact the system. 

From this part of the system onwards all operations were completed inside the main loop() of the Arduino script. Because of this greater structure was needed and the FSM became more crucial. State 2 of the system is straight forward. In this state the Romi is instructed to move forward and then to evaluate whether it has yet reached the line. It does this at each iteration of the loop. If the evaluation of the line results in a positive conrmation that it has reached the line, then the Romi proceeds to State 3. If evaluation determines that the line has not yet been reached the line, the Romi proceeds to move forward the following iteration. 

State 3 is the state which contains the largest number of operations and is where the operations of the main crux of the line following occurs. The role of this state is for the Romi to stay on the line, turning appropriately when required and evaluating whether the end of the line has been reached. The core of this state revolves around the use of a weighted controller. The weighted controller in this case is in eect a proportional controller, adjusting the speed at which the Romi should turn depending on the extremity of the dierence between the left and right sensor readings. Further to ensure that the Romi did not get stuck constantly turning in alternating clockwise and anticlockwise directions a forward bias clause was added. The aim of this was to enable to Romi to continue in a forward motion if the relative dierence between the left and right IR sensor reading was small and would only begin turning again when the dierence between the two values had exceeded a particular threshold. This can be seen by can be seen illustrated in Figure 1.1 above with 3 dierent motion outcomes depending on the Weighted average value (M). Within Stage 3 once motion had been applied, the "On line" condence value is updated by evaluating a conditional statement regarding the values of the left, centre and right IR sensors. If the result of the conditional statement is "on the line" then +1 is added to condence value, if the result is "o the line" the condence value is halved. At the end of each iteration within state 3 this is evaluated, if the condence value is now below the threshold value then the operation mode of the Romi will now be state 4, if not then the Romi will recalculate the weighted average M value and repeat the previous steps, as can be seen in Figure 1. 

Stage 4 is the completion stage. Once the Romi has reached this stage it will have reached the end of the line if the operation of the Romi has worked correctly. once it has reached this stage, the Romi stops and completes an on o series of buzzer noises to demonstrate that completion has occurred before outputting a "Task Complete" string to the user.
