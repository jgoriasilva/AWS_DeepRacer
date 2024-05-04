# Follow middle fine-tuning speed and progress

For this model, I cloned the FM-FT-Continuous model and fine-tuned it using a reward on being faster and obtaining more progress.

This is a very similar model to the FM-FT-Speed-Progress, but I believe it to be conceptually more correct, since it gives reward on the **ratio** between progress and steps, and not only progress. More details about it on the reward function.

## Action space

To help the model to be faster, I increased all speeds by about 0.20, which is the same action space used by FM-FT-Speed-Progress.

|Steering angle (°)|Speed (m/s)|
|---|---|
|-30.0|1.20|
|-15.0|1.30|
|-15.0|2.20|
|30.0|1.20|
|15.0|1.30|
|15.0|2.20|
|0.0|2.70|
|0.0|4.00|

## Reward function

As for the FM-FT-Speed-Progress, I modified the reward function built before to include a reward on speed and progress.

Contrary to the FM-FT-Speed-Progress, however, the reward on progress is given by a ratio between progress and steps, multiplied the ideal number of max steps 100% progress. This value of max steps was taken simply from a log analysis on the best races, by seeing what was the minimum number of steps required by the previous model to finish the race. The idea is to make the model be more efficient on its steps i.e. choose the steps that lead to a better progress.

The final reward is still normalized by taking a weighted average from the four rewards, putting more weights on the speed and progress.

```python
def reward_function(params):
    # Read input parameters
    distance_from_center = params['distance_from_center']
    track_width = params['track_width']
    abs_steering = abs(params['steering_angle']) # Only need the absolute steering angle
    speed = params['speed']
    progress = params['progress']
    all_wheels_on_track = params['all_wheels_on_track']
    steps = params['steps']

    distance_reward = 1 - (distance_from_center/track_width*0.5)**0.5  
    steering_reward =  1 - (abs_steering/30)**0.5
    speed_reward = (speed/4.0)**2
    
    MAX_STEPS = 160
    
    if steps > 0: # undefined if steps == 0
        progress_reward = progress/steps * 160/100 # scaled by MAX_STEPS / MAX_PROGRESS
    else:
        progress_reward = 0
    
    reward = (distance_reward + steering_reward + 2*speed_reward + 2*progress_reward) / 6
    
    if not all_wheels_on_track:
        reward *= 0.1
    
    return float(reward)
```

## Hyperparameters

Same as FM-FT-Speed-Progress: reduced learning rate from 5e-4 to 1e-5 so that the optimization can happen close to the optimal point found by the previous model (FM-FT-Continuous).

|Hyperparameter|Value|
|---|---|
|Gradient descent batch size|64|
|Entropy|0.01|
|Discount factor|0.98|
|Loss type|Huber|
|Learning rate|0.00001|
|Episodes between each iteration|32|
|Number of epochs|5|

## Training

I trained this model for a total time of 45 minutes. Below is the best model:

|Iteration|Avg completion - eval|Avg completion - train|Avg reward - train|
|---|---|---|---|
|9|88.6%|49.40|44.28|

There is a slight decrease in average completion, indicating a less stable model. That is ok for now, since we were aiming on improving the speed.

## Results and conclusion

This model obtained the following performance:

|Time|Race time|Resets|
|---|---|---|
|00:08.801|00:33.926|2|

The result on this one was much better than the previous models! Unfortunately, the model was very unstable, which made it get off track twice. Although more races could be tried, I decided on keep improving the stability of the model instead of just trying to race again. As well, I decided on keep using this model from now on instead of the FM-FT-Speed-Progress because it is conceptually more correct.