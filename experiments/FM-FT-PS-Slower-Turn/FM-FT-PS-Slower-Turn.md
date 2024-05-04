# Slower Turn

For this model, I cloned the FM-FT-Speed-Progress-Steps model and fine-tuned it while reducing its speed on curves. The hope was to increase its stability and allow it to turn slower, since that's what was causing it to get off track all the time. I also increased the speed by a bit on 0 degrees and 15 degrees.

## Action space

As discussed, the previous model was not able to do the sharp curves, so I decreased the speed for a steering angle of 30°. I also increased the speed for 0° and 15° to make it go faster while going forward. 

|Steering angle (°)|Speed (m/s)|
|---|---|
|-30.0|0.80|
|-15.0|1.30|
|-15.0|2.30|
|30.0|0.80|
|15.0|1.30|
|15.0|2.30|
|0.0|2.90|
|0.0|4.00|

## Reward function

I used a very similar reward function from the previous model (FM-FT-Speed-Progress-Steps), but I reduced the max_steps parameters to make it go even faster. I reduced the reward on speed and progress to increase the stability of the model, putting more weight on following the center.

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
    
    MAX_STEPS, MAX_PROGRESS = 150, 100
    
    if steps > 0: # undefined if steps == 0
        progress_reward = progress/steps * MAX_STEPS/MAX_PROGRESS # scaled by MAX_STEPS / MAX_PROGRESS
    else:
        progress_reward = 0
    
    reward = (distance_reward + steering_reward + speed_reward + progress_reward) / 4
    
    if not all_wheels_on_track:
        reward *= 0.1
    
    return float(reward)
```

## Hyperparameters

I kept the learning rate of 1e-5 so that the optimization can happen close to the optimal point found by the previous model. For this one, I experimented with modifying the values of episodes between each iteration and the number of epochs so that the model learns faster.

|Hyperparameter|Value|
|---|---|
|Gradient descent batch size|64|
|Entropy|0.01|
|Discount factor|0.98|
|Loss type|Huber|
|Learning rate|0.00001|
|Episodes between each iteration|16|
|Number of epochs|10|

## Training

I trained this model for a total time of 45 minutes. Below is the best model:

|Iteration|Avg completion - eval|Avg completion - train|Avg reward - train|
|---|---|---|---|
|9|97.8%|68%|59.87|

We see an increase in average completion, correlated with the increased reward. This indicates that the strategy worked very well.

## Results and conclusion

This model obtained the following performance:

|Time|Race time|Resets|
|---|---|---|
|00:08.598|00:27.065|2|

This was a great improvement from the previous model. The model kept its high speed, while being much more stable. It did take however two tries to get this performance.