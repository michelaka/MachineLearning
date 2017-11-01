# MachineLearning
A collection of python Machine Learning articles and examples. You will find code related to MDP, Bellman, OpenAI solutions and others

*Michel is an AI researcher and a graduate from University of Montreal*

## Solving Cartpole the right way

Our solution of the Open AI Cartpole combines many different aspects of Machine Learning to solve the problem in the most modular, 
yet efficient way possible.

Our solution learns by observing the first 100 games, then trains a sequential model made of 2 stateful LSTMs, one relu and a regular dense model for output.
Once the model is trained, we do not store any discrete information about the environment. 
We first teach the model to anticipate the next state of the environment give any plausible action. In essence, the LSTMs learn that
given a series of event (states + actions) this next state and reward are most probable.

Once our model can anticipate future states correctly, even for event it has never encountered, we used it in an MDP, solved using a Bellman approach 
(utility/reward at state 1 = reward + sum of discounted expected reward). The twist here is that we recursively calculate expected reward using the LSTM models which 
is now adept at anticipating future states and rewards.

```
    # Bellman -- reward at this state = reward + Sum of discounted expected rewards for all actions (recursively)
    #recursively calculate the reward at future states for all possible actions
    discounted_future_rewards = 0.95*estimateReward(expected_state,0,depth-1)+ 0.95*estimateReward(expected_state,1,depth-1)

    #print("discounted_future_rewards", discounted_future_rewards)
    #add current state and discounted future state reward
    return reward + discounted_future_rewards
```

With the above we can then determine which action to take at any given state.
```
        #chose an action by estimating consequences of actions 5 steps ahead
        #works best with looking 6 steps ahead
        #Also works best if you train the model more itterations
        estimated_anticipated_reward_a = estimateReward(qs,1,num_anticipation_steps)
        estimated_anticipated_reward_b = estimateReward(qs,0,num_anticipation_steps)
        print(" estimated rewards a and b", estimated_anticipated_reward_a, estimated_anticipated_reward_b)

        #chose argmax action of estimated anticipated rewards
        if estimated_anticipated_reward_a > estimated_anticipated_reward_b:
            a = 1
        else:
            a = 0
```

this has proven remarkably successfull as the model is able to balance the pole indefinately.

We make use of the following:
* Markov Decision Process
* Bellman equation
* Non discrete/ Non-linear approach to Bellman
* Anticipation / imagination
* sequence to sequence learning with LSTM
* Environment observation

### what you will need to run our Cartpole
You will need a working version of python. Preferably 3.6 or above with the following libraries. 
`Pip install <library name>` should get you going.

```
import numpy as np
import keras
import gym
import os
import h5py
```

Make sure you also set `observe_and_train` to True the first time you run the model. After the first run, the model will save the weights for future use so you can set it back to False. You will find this variable along with other variables you can tweak right after the header/import section of the file.

### possible improvements
Many... So many...

### Please comment and provide feedback.
