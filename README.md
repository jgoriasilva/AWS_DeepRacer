# AWS DeepRacer experience

This repo shares my experiments with AWS DeepRacer throughout the classification phase for the CSBC 2024.

## About AWS DeepRacer

AWS DeepRacer is a competition where you train a small race car to self-driving using reinforcement learning. It is available on the AWS console.

## About reinforcement learning and self-driving cars

Reinforcement learning is what powers AWS DeepRacer. It’s a way for the car to learn by trying things out and seeing what works best. This helps the car make decisions on its own as it drives around.

In this repo I’ll share what I’ve learned with my experiences, including hyperparameters tuning, reward functions and other tips. 

I hope this can help you on your own journey in the world of autonomous racing.

## Experiments

In the folder `experiments` I have summarized some key insights from the most interesting experiments, which are described below.

|Iteration|Model codename|Idea|
|---|---|---|
|1|[FollowMiddle](https://github.com/jgoriasilva/AWS_DeepRacer/blob/main/experiments/FollowMiddle/FollowMiddle.md)|Follow the middle and penalize for steering.|
|2|[FM-FT-Continuous](https://github.com/jgoriasilva/AWS_DeepRacer/blob/main/experiments/FM-FT-Continuous/FM-FT-Continuous.md)|Fine-tune previous model using a continuous reward function.