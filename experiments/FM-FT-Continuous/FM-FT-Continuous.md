# Follow middle fine-tuning continuous

For this model, I cloned the FollowMiddle model and fine-tuned it using a continuous reward function.

## Action space

To make the model faster, I increased the speed on steering angle 0.

|Steering angle (°)|Speed (m/s)|
|---|---|
|-30.0|1.00|
|-15.0|1.00|
|-15.0|2.00|
|30.0|1.00|
|15.0|1.00|
|15.0|2.00|
|0.0|2.50|
|0.0|4.00|

## Reward function

I built my own reward function that was inspired by the one suggested by AWS. This one uses a continuous non-linear function to reward for closeness to the center and low steering, which makes the model learn better. It also normalizes the final reward, keeping it between 0 and 1. This is important because makes it easier to analyze the correlation between reward and performance.

```
def reward_function(params):

    # Read input parameters
    distance_from_center = params['distance_from_center']
    track_width = params['track_width']
    abs_steering = abs(params['steering_angle']) # Only need the absolute steering angle
    all_wheels_on_track = params['all_wheels_on_track']

    distance_reward = 1 - (distance_from_center/track_width*0.5)**0.5  
    steering_reward =  1 - (abs_steering/30)**0.5
    
    reward = (distance_reward + steering_reward) / 2
    
    if not all_wheels_on_track:
        reward *= 0.1
    
    return float(reward)
```

## Hyperparameters

Reduced learning rate so that the optimization can happen close to the optimal point found by the base model (FollowMiddle).

|Hyperparameter|Value|
|---|---|
|Gradient descent batch size|64|
|Entropy|0.01|
|Discount factor|0.98|
|Loss type|Huber|
|Learning rate|0.00005|
|Episodes between each iteration|32|
|Number of epochs|5|

## Training

I trained the model with a maximum time of 30 minutes. Below is the best model:

|Iteration|Avg completion - eval|Avg completion - train|Avg reward - train|
|---|---|---|---|
|6|98%|44.28|46.59|

We see a better average completion, correlated with a higher reward, which shows that we're on a good track.

## Results and conclusion

This model obtained the following performance:

|Time|Race time|Resets|
|---|---|---|
|00:09.591|00:30.660|0|

This is a great performance improvement compared to the previous model, with no reset and much faster.