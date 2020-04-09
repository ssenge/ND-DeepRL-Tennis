### Project Details

* The state space has 24 dimensions, the action space 4 dimensions.
* The learning algorithm is based on Udacity's DDPG implementation for the bipedal problem. The agent.py and model.py files were almost unchanged, however, I tried multiple model parameters manually. The solution was found in 2085 episodes. 

### Implementation details

The agent uses the Deep Deterministic Policy Gradients (DDPG) algorithm with two agents as RL framework, which exhibits the following characteristics:

* Policy-based: A functional approximation approach is used to directly estimate iteratively an optimal policy (in contrast to value based methods). In this case a simple neural network is used as approximator.
* Actor/Critic: Policy-based approaches are known to have a low bias but significant high variance. Value-based approaches, however, tend to have lower variance but worse bias. Combining two neural networks, one policy-based (low bias, actor: calculates an action policy) and one value-based (low variance, critic: calculates the (continuous) action's magnitude), usually also leads to a combination of these two benefits.

The involved components:

* Actor network: Used to decide between the actions (4), based on the bipedal sample code, 3 fully connected layers (state_size=24 -> 128 -> 128 -> action_size=2) are used, hence the output layer size equals the action space and hyperbolic tangens activation function is used to derive whether this action shall be selected or not. After a lot of manual experimenting, it turned out that batch normalization and clipping (as discussed in the classroom samples) helped to converge much quicker. Batch normalization after layer 1 (but not after layer 2) seems absolutely crucial for the algorithm to learn. Without it scores most of the time wasn't higher than 2. The other hyperparameters are basically the same as in the sample code, with the exception that I noticed that even small changes in the learning rate has significant impact. My final value is 2e-4 which works fine but just the small change to 1e-4 does not lead to a solution.
* Critic network: Used to derive a value for the action (continuous), based on the bipedal sample code, 3 fully connected layers (state_size=24 -> 128 + action_size=2 -> 128 -> 1) are used, hence a single output node is used (without any activation function to get the direct continuous response). Here as well, batch normalization turned out to be effective. The other hyperparameters are basically the same as in the sample code.
* DDPG algorithm: The implementation is very similar to Udacity's Bipedal sample code. Per episode, time steps are conducted as long as the ball does not hit the floor. In each time step, the agent acts accordingly to the current state and hence results in a new action, which in turn is used to get a new state and result from the environment. In each time step, the experienced action, state, reward pairs are stored in a replay buffer and finally evaluated and the network weights are updated according to the DDPG update rules as explained in the lessons.

### Potential improvements
    * A systematic search for optimal hyperparameters and dropout or network architecture could be implemented (e.g. using the Hyperopt package)
    * Prioritized Experience Replay is also likely to speed up the learning process
    * The algorithms PPO and Multi Agent DDPG are likely expected to yield better results
