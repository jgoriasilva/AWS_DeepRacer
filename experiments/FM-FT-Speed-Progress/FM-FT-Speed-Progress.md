# Follow middle fine-tuning speed and progress

For this model, I cloned the FM-FT-Continuous model and fine-tuned it using a reward on being faster and obtaining more progress.

## Action space

To help the model to be faster, I increased all speeds by about 0.20

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

I modified the reward function built before to include a reward on speed and progress. For this model, the reward on progress was on the total percentage of progress. The final reward is still normalized by taking a weighted average from the four rewards, putting more weights on the speed and progress.

```python
def reward_function(params):
    # Read input parameters
    distance_from_center = params['distance_from_center']
    track_width = params['track_width']
    abs_steering = abs(params['steering_angle']) # Only need the absolute steering angle
    speed = params['speed']
    progress = params['progress']
    all_wheels_on_track = params['all_wheels_on_track']

    distance_reward = 1 - (distance_from_center/track_width*0.5)**0.5  
    steering_reward =  1 - (abs_steering/30)**0.5
    speed_reward = (speed/4.0)**2
    progress_reward = progress/100
    
    reward = (distance_reward + steering_reward + 2*speed_reward + 2*progress_reward) / 6
    
    if not all_wheels_on_track:
        reward *= 0.1
    
    return float(reward)
```

## Hyperparameters

Reduced learning rate from 5e-4 to 1e-4 so that the optimization can happen close to the optimal point found by the previous model (FM-FT-Continuous)

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

Again I trained the model with a maximum time of 30 minutes. Below is the best model:

|Iteration|Avg completion - eval|Avg completion - train|Avg reward - train|
|---|---|---|---|
|6|100%|42.625|24.66|

There is a slight increase in average completion, for a slightly lower reward. That is ok since we changed the reward function.

## Results and conclusion

This model obtained the following performance:

|Time|Race time|Resets|
|---|---|---|
|00:08.065|00:29.469|1|

The result on this one was much better than the previous models! Unfortunately, the model was still quite unstable, which made it get off track once. Although more races could be tried, I decided on keep improving the stability of the model instead of just trying to race again.