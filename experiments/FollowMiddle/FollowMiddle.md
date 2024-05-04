# Follow middle

The first experiment had a simple objective: teach the car to follow the center of the track.

## Action space

I opted for a simple discrete action space, which allows for higher speeds on lower angles. This action space is simple enough so that the model can certainly finish the track while also allowing a quicker optimization.

|Steering angle (°)|Speed (m/s)|
|---|---|
|-30.0|1.00|
|-15.0|1.00|
|-15.0|2.00|
|30.0|1.00|
|15.0|1.00|
|15.0|2.00|
|0.0|2.00|
|0.0|3.00|

## Reward function

I used the suggested reward function from AWS:

```python
def reward_function(params):

    # Read input parameters
    distance_from_center = params['distance_from_center']
    track_width = params['track_width']
    abs_steering = abs(params['steering_angle']) # Only need the absolute steering angle

    # Calculate 3 marks that are farther and father away from the center line
    marker_1 = 0.1 * track_width
    marker_2 = 0.25 * track_width
    marker_3 = 0.5 * track_width

    # Give higher reward if the car is closer to center line and vice versa
    if distance_from_center <= marker_1:
        reward = 1.0
    elif distance_from_center <= marker_2:
        reward = 0.5
    elif distance_from_center <= marker_3:
        reward = 0.1
    else:
        reward = 1e-3  # likely crashed/ close to off track

    # Steering penality threshold, change the number based on your action space setting
    ABS_STEERING_THRESHOLD = 15 
    
    # Penalize reward if the car is steering too much
    if abs_steering > ABS_STEERING_THRESHOLD:
        reward *= 0.8
    return float(reward)
```

This function will reward the car for following the center of the track, and it will penalize the car (reduce reward) if it is steering too much.

## Hyperparameters

The hyperparameters used are mostly default. The big learning rate allows for a quicker optimization, while the bigger episodes between each iteration makes the training more stable.

|Hyperparameter|Value|
|---|---|
|Gradient descent batch size|64|
|Entropy|0.01|
|Discount factor|0.98|
|Loss type|Huber|
|Learning rate|0.0003|
|Episodes between each iteration|32|
|Number of epochs|5|

## Training

I trained the model with a maximum time of 30 minutes. Below is the best model:

|Iteration|Avg completion - eval|Avg completion - train|Avg reward - train|
|---|---|---|---|
|13|92%|24.22|29.34|

I trained the model for 30 minutes. The best model was obtained at 13 iterations

## Results and conclusion

This model obtained the following performance:

|Time|Race time|Resets|
|---|---|---|
|00:12.269|00:40.796|1|

The model was capable of finishing the track in a very slow time, including one reset. It provided me with a good base to work on. 