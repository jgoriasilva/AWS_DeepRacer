# Slower Turn Faster

For this model, I cloned the FM-FT-PS-Slower-Turn model and fine-tuned it to increase its speed. The hope was to use the more stable previous model and make it faster.

## Action space

I increased the speeds except for the sharp turn, to keep its stability while making it faster.

|Steering angle (°)|Speed (m/s)|
|---|---|
|-30.0|0.80|
|-15.0|1.40|
|-15.0|2.40|
|30.0|0.80|
|15.0|1.40|
|15.0|2.40|
|0.0|3.00|
|0.0|4.00|

## Reward function

Very similar reward function from the previous model, with even more reduced max_steps parameter, incentivizing the model to take even more efficient steps.

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
    
    MAX_STEPS, MAX_PROGRESS = 130, 100
    
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

I reduced the learning rate from 1e-5 to 5e-6 so that the optimization can happen close to the optimal point found by the previous model.

|Hyperparameter|Value|
|---|---|
|Gradient descent batch size|64|
|Entropy|0.01|
|Discount factor|0.98|
|Loss type|Huber|
|Learning rate|0.000005|
|Episodes between each iteration|16|
|Number of epochs|10|

## Training

I trained this model for a total time of 30 minutes. Below is the best model:

|Iteration|Avg completion - eval|Avg completion - train|Avg reward - train|
|---|---|---|---|
|3|100%|59%|48.82|

We see an increase in average completion (on evaluation), for lower rewards and completion on training. This is fine since we're demanding more from the model by setting an ambitious max_steps parameter.

## Results and conclusion

This model obtained the following performance:

|Time|Race time|Resets|
|---|---|---|
|00:08.403|00:26.059|0|

This was the best performance so far, and it put me in the first place of the competition. It took two tries to get this performance. The second best was 00:26.976, which still got the first place.