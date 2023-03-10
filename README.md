# Snake-DQN

For this project, I'll be using PyTorch as the neural network library of choice.

### Environment

The environment consists of a 50x50 array of values, each of which is coded such that it represents different components of the game. For example, 1s are the body of the snake, 2 is the apple (or whatever it is the snake eats), and everything else is 0. With this, I can have a full representation of the game states in a single, concise array.

### Observations

Observations for our agent will be 12-dimensional binary array of features for the directions of danger and food. The first 8 features indicate dangers around the snake's head. There are 8 of these because there are 8 positions *around* the snake's head. This means that the snake is very nearsighted (since the features only show directions of danger out to one unit from the head of the snake), but I just wanted to see how well the agent would do with this as a starting point, I might expand on this in the future. 

Also, the last 4 features just point to the food. If the food is at a diagonal position from the head, then 2 of these features will be active.

### Reward Function:

Pretty simple: +10 for each apple the snake eats or -10 if the snake dies. I tried more complicated reward functions, but this seems to do better while being simpler.

### Model

For an environment and observations as simple as I have outlined, I decided to use a pretty light network architecture. There are 12 input units, 2 hidden layers: the first has 256 units and the second has 128, then 4 output units for the 4 possible actions available in the action-space. The hidden layers consist of ReLu-activated units. This makes for a total of 36740 parameters. 

The loss function I used was the simple mean-squared-error. 

Finally, for the optimizer, I decided to use AdamW in PyTorch since this seems to have subsumed the original Adam optimizer since the publication of the paper by Ilya Loshchilov and Frank Hutter (https://arxiv.org/abs/1711.05101), on arxiv, detailing the errors with the Adam gradient descent procedure.

## Training

For training, I used an experience replay buffer to sample random state-action pairs to use for training in a supervised setting. This seems to be the standard approach to utilizing neural network in reinforcement learning applications since it decorrelates the state-action pairs from one another, rendering them (closer) to i.i.d.

To train the model, I read about a sort of bootstrapping procedure to increase stability of training. This entails training two networks at once (another reason for the light network architecture): a target network and a training network. The training network is used to generate current guesses for the q-values: it is updated at every timestep. The target network gets updated every 5 episodes with the weights of the training network. The reason for this is to stablize the predictions of future q-values when used to optimize the training network. This gives us labels to compute a loss for optimizing the network.

I trained the network for a total of 10000 episodes overnight (~ 5 hours of training), below are the results.

## Results

Here is the agent, after 10000 games under its belt, getting itself out of some pretty sticky situations:


https://user-images.githubusercontent.com/39159387/215646438-cc913b52-aa73-4d87-ab57-ea144e63e7ee.mp4


Admittedly, there's a clear bug in my game code which allows food to spawn inside of the snake's body, but it's impressive to see the agent work around this (at least that's my excuse).
