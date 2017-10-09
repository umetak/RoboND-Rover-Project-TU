## Project: Search and Sample Return
### Source codes are located in `./code` folder.

---


**The goals / steps of this project are the following:**  

**Training / Calibration**  

* Download the simulator and take data in "Training Mode"
* Test out the functions in the Jupyter Notebook provided
* Add functions to detect obstacles and samples of interest (golden rocks)
* Fill in the `process_image()` function with the appropriate image processing steps (perspective transform, color threshold etc.) to get from raw images to a map.  The `output_image` you create in this step should demonstrate that your mapping pipeline works.
* Use `moviepy` to process the images in your saved dataset with the `process_image()` function.  Include the video you produce as part of your submission.

**Autonomous Navigation / Mapping**

* Fill in the `perception_step()` function within the `perception.py` script with the appropriate image processing functions to create a map and update `Rover()` data (similar to what you did with `process_image()` in the notebook).
* Fill in the `decision_step()` function within the `decision.py` script with conditional statements that take into consideration the outputs of the `perception_step()` in deciding how to issue throttle, brake and steering commands.
* Iterate on your perception and decision function until your rover does a reasonable (need to define metric) job of navigating and mapping.  

[//]: # (Image References)

[image1]: ./misc/fig1.png
[image2]: ./misc/fig2.png
[image3]: ./misc/fig3.png

## [Rubric](https://review.udacity.com/#!/rubrics/916/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Notebook Analysis
#### 1. Run the functions provided in the notebook on test images (first with the test data provided, next on data you have recorded). Add/modify functions to allow for color selection of obstacles and rock samples.

The color thresholding is applied to get the pixel locations of ground, obstacles, and golden rocks.
The threshold value for the ground is [160, 160, 160] of RGB. I used a given default value.
For the obstacles, I just inverted the ground image.
For golden rocks, I convert a RGB image to a HSV image. As the threshold value, I chose [20-30, 90, 90] of HSV because the golden(yellow) color nearly stays between 20 and 30 of H value.

Threshold processed images shown below.
Top Left: Input image,  Top Right: Ground pixels  Bottom Left: Obstacles pixels  Bottom Right: Rock pixels

![alt text][image1]

#### 2. Populate the `process_image()` function with the appropriate analysis steps to map pixels identifying navigable terrain, obstacles and rock samples into a worldmap.  Run `process_image()` on your test data using the `moviepy` functions provided to create video output of your result.

For the video, blue color shows ground, red color shows obstacles, and yellow shows rocks, respectively.
I used different previous_value for these three thresholding images to update the world map.
I assigned +1 for the ground but +10 for obstacles and rocks because it works better to update maps during test runs.   
A screenshot from a video of the processed results of my recording is shown below.

![alt text][image2]

### Autonomous Navigation and Mapping

#### 1. Fill in the `perception_step()` (at the bottom of the `perception.py` script) and `decision_step()` (in `decision.py`) functions in the autonomous mapping scripts and an explanation is provided in the writeup of how and why these functions were modified as they were.
`perception_step()` is almost same as the function `process_image()` in the ipython notebook.

To optimize map fidelity, perspective transform is technically only valid when roll and pitch angles are near zero.
Therefore I altered codes like below.
```
if (Rover.pitch>359.0 or Rover.pitch<1.0) and (Rover.roll>359.0 or Rover.roll<1.0):
    Rover.worldmap[obstacles_y_world, obstacles_x_world, 0] += 1
    Rover.worldmap[rocks_y_world, rocks_x_world, 1] += 5
    Rover.worldmap[terrain_y_world, terrain_x_world, 2] += 5
```

In addition, I altered `if clause` in `decision_step()` for the picking up action. The rover seems to never stop around the golden rocks, so I gave another state when the rover is near objective.

#### 2. Launching in autonomous mode your rover can navigate and map autonomously.  Explain your results and how you might improve them in your writeup.  

**Note: running the simulator with different choices of resolution and graphics quality may produce different results, particularly on different machines!  Make a note of your simulator settings (resolution and graphics quality set on launch) and frames per second (FPS output to terminal by `drive_rover.py`) in your writeup when you submit the project so your reviewer can reproduce your results.**

In my environment, resolution is 1028x768, graphics quality is about 60 FPS.

I did several trial runs, and then I noticed there are two big problems.
The one is "loop" problem. Because of the poor direction making, the rover often run around on the same route repeatedly. And the rover never try to boldly go the frontier. So the rover needs to get more decision making state.
The another is "stuck" problem. If the rover unfortunately dive into a narrow space, it can not figure out how to escape from such space. The rover seems to try something but nothing happens. So the rover needs any other action state for this problem
 

![alt text][image3]
