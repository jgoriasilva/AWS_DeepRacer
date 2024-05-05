# Faster Steering Progress Speed

For this model, I cloned the FM-FT-PS-Slower-Turn-Faster model and fine-tuned it to increase its speed. The goal was to incentivize the Faster model to steer less and keep going straight, specially on straight lines.

## Action space

I **decreased** the speed for 15 degrees, and **increased** it for 30 degrees. The idea is to make the model choose 15 degrees less, while making it turn quicker on the sharp turn.

|Steering angle (°)|Speed (m/s)|
|---|---|
|-30.0|0.90|
|-15.0|1.40|
|-15.0|2.30|
|30.0|0.90|
|15.0|1.40|
|15.0|2.30|
|0.0|3.00|
|0.0|4.00|

## Reward function

Very similar reward function from the previous model. For this one, I removed the max_steps parameter, removed the reward on following the center of the track, and reduced the penality for being on the edge of the track, incentivizing the model to look for shortcuts on the track.

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
    
    steering_reward =  1 - (abs_steering/30)**0.5
    speed_reward = (speed/4.0)**2
    
    if steps > 0: # undefined if steps == 0
        progress_reward = progress/steps
    else:
        progress_reward = 0
    
    reward = (steering_reward + speed_reward + progress_reward) / 3
    
    if not all_wheels_on_track:
        reward *= 0.5
    
    return float(reward)
```

## Hyperparameters

I reduced the learning rate from 5e-6 to 1e-6 so that the optimization can happen close to the optimal point found by the previous model.

|Hyperparameter|Value|
|---|---|
|Gradient descent batch size|64|
|Entropy|0.01|
|Discount factor|0.98|
|Loss type|Huber|
|Learning rate|0.000001|
|Episodes between each iteration|16|
|Number of epochs|10|

## Training

I trained this model for a total of one hour. Below is the best model:

|Iteration|Avg completion - eval|Avg completion - train|Avg reward - train|
|---|---|---|---|
|8|100%|76.31%|48.63|

This model is remarkably stable on training and evaluation. In fact, the last four iterations presented a 100% completion on evaluation. The reward was a bit unstable but increased at the end of the training.

## Results and conclusion

This model obtained the following performance:

|Time|Race time|Resets|
|---|---|---|
|00:08.332|00:25.856|0|

This was the best performance so far, improving the score of the previous model "Faster", and it put me in the first place of the competition.

I suppose a next try would be to increase its speed on straight lines and punish it even more for steering too much, train for 30 minutes and see if there is an improvement without being too unstable.