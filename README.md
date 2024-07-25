# AWS DeepRacer Experience
Welcome to my AWS DeepRacer repository, where I share my journey and experiments during the classification phase for the CSBC 2024.

## About AWS DeepRacer
AWS DeepRacer is a competition that challenges you to train a miniature race car to drive autonomously using reinforcement learning. This competition is accessible through the AWS console, providing a hands-on experience to self-driving car technology and reinforcement learning.

## About Reinforcement Learning and Self-Driving Cars
Reinforcement learning, the core of AWS DeepRacer, allows the car to learn optimal driving strategies through trial and error. By receiving feedback from its actions, the car continuously improves its decision-making skills to navigate the track independently.

## What You'll Find in This Repository
In this repository, I share my insights and experiences from participating in the AWS DeepRacer competition. This includes:

- Hyperparameter Tuning: How to better adjust the parameters to optimize the performance of the model.
- Reward Functions: How to design better reward functions that guide the car's learning process effectively.
- Tips and Tricks: Practical advice and strategies that I found useful during my experimentation.

I hope this repository serves as a valuable resource for anyone interested in autonomous racing and reinforcement learning.

## Experiments

In the folder `experiments` I have summarized some key insights from the most interesting experiments, which are presented below.

|Iteration|Model codename|Idea|
|---|---|---|
|1|[FollowMiddle](https://github.com/jgoriasilva/AWS_DeepRacer/blob/main/experiments/FollowMiddle/FollowMiddle.md)|Follow the middle and penalize for steering.|
|2|[FM-FT-Continuous](https://github.com/jgoriasilva/AWS_DeepRacer/blob/main/experiments/FM-FT-Continuous/FM-FT-Continuous.md)|Fine-tune previous model using a continuous reward function.
|3|[FM-FT-Speed-Progress](https://github.com/jgoriasilva/AWS_DeepRacer/blob/main/experiments/FM-FT-Speed-Progress/FM-FT-Speed-Progress.md)|Fine-tune FM-FT-Continuous on speed and progress.
|4|[FM-FT-Speed-Progress-Steps](https://github.com/jgoriasilva/AWS_DeepRacer/blob/main/experiments/FM-FT-Speed-Progress-Steps/FM-FT-Speed-Progress-Steps.md)|Fine-tune FM-FT-Continuous on speed and ratio between progress and steps.
|5|[FM-FT-PS-Slower-Turn](https://github.com/jgoriasilva/AWS_DeepRacer/blob/main/experiments/FM-FT-PS-Slower-Turn/FM-FT-PS-Slower-Turn.md)|Fine-tune FM-FT-Speed-Progress-Steps by reducing its speed on sharp turns to increase stability.
|6|[FM-FT-PS-Slower-Turn-Faster](https://github.com/jgoriasilva/AWS_DeepRacer/blob/main/experiments/FM-FT-PS-Slower-Turn-Faster/FM-FT-PS-Slower-Turn-Faster.md)|Fine-tune FM-FT-PS-Slower-Turn by increasing its speed.
|7|[FM-FT-PS-Slower-Turn-Faster-2](https://github.com/jgoriasilva/AWS_DeepRacer/blob/main/experiments/FM-FT-PS-Slower-Turn-Faster-2/FM-FT-PS-Slower-Turn-Faster-2.md)|Fine-tune FM-FT-PS-Slower-Turn-Faster by increasing its speed.
|8|[Faster-SteeringSpeedProgress](https://github.com/jgoriasilva/AWS_DeepRacer/blob/main/experiments/Faster-SteeringProgressSpeed/Faster-SteeringProgressSpeed.md)|Fien-tune FM-FT-PS-Slower-Turn-Faster by reducing its speed on 15 degrees and increasing its speed on 0 degrees. Removed reward for following the center, reduced punishment for being off track.|
