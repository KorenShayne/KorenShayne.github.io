# what are you reading
hi, i'm koren.

this is a write-up about my senior project on reinforcement learning.

# why
i've been interested in reinforcement learning since hearing about its success specifically with playing DOTA and StarCraft II.

after taking a course on neural networks where we spent one week on reinforcement learning, i wanted to take a deeper dive and try to learn more of the math behind reinforcement learning and try to apply it in a simple case.

# reading i did

first, i read *Foundations of Deep Reinforcement Learning: Theory and Practice in Python* by Laura Graesser (Author) and Wah Loon Keng. it was fine, but all of the examples needed to run on linux (it was a pain to set up WSL), and i didn't retain that much from reading through their code. it felt like a tutorial for using their reinforcement learning codebase.

then, i read the first section (tabular methods) and a small part of the second section of *Reinforcement Learning, an Introduction* by by Andrew Barto and Richard S. Sutton. i learned so much from this book, and the entire project is built off of the algorithms described in this book. ALSO it's free. i would highly recommend this book if you like math want to read about reinforcement learning.

# idea
my original idea was to build a reinforcement learning agent to play Magic: the Gathering, or some reduced version of it. i quickly learned that this would be very challenging, and the methods I learned about would not be applicable. also, creating a rules engine for Magic is not simple.

my second idea was to solve 2x2 rubik's cubes. this is what i did.

# what is reinforcement learning?

reinforcement learning is both a class of problems and a class of solutions to those problems.


## problems
a reinforcement learning problem has a few important elements
- states
- actions
- rewards

an agent existing in some state can take an action in order to move to a new state and recieve some reward. these dynamics of how actions move between states and how rewards are given is described by the environment.

the list of all possible states is called the state space.

the list of all possible actions is called the action space.

the rewards are just numbers. they can be negative.

## solutions
the goal of a reinforcement learning algorithm is to build a policy such that an agent following this policy takes actions which maximize the amount of reward it will recieve.

i mostly worked within the case that there is a finite number of states and that this number is small enough that you can reasonably store them all in memory. this is the reason why i chose a 2x2 rubik's cube over a 3x3.

**policy evaluation** is the act of correctly determining the value of each state with respect to the current policy. 

**policy improvement** is the act of exchanging an old policy with a new one such that in every situation the new policy chooses actions which give at least and possibly more reward than the previous policy.

in order to find an optimal policy, first assign an arbitrary value to each state and a policy which chooses some arbitrary action in each state. 

- for each state perform policy evaluation to update its value to be equal to the immediate reward plus the a slightly discounted value of the state you end up in.
- perform policy improvement by exchanging the current policy with one which selects the action that leads to the highest value states

alternate between these two steps until the values in one iteration are sufficiently close to the values in the next iteration.

# rubik's cubes

i chose to represent a rubik's cube as a 4x6 numpy array where each entry is an integer representing the color of a single face.

the first challenge i ran into was finding a way to generate all possible states of the rubik's cube. it is not as simple as just choosing all possible 4x6 arrays with values 0-5 because this contains many arrays which are not legal states, such as an array of all 0s which would represent a cube with every face white. additionally, the cube is somewhat symmetric, and it is important to reduce the number of states using all available symmetries to be able to contain all of them in memory.

the symmetries ended up being much simpler to solve than i expected. it is enough to simply fix a single corner and only allow rotation of 3 of the faces. due to this, the action space is (R, R', F, F', U, U') where R, F, and U represent a 90 degree clockwise rotation of the right, front, or top face respectively and R', F', and U' represent their counter-clockwise rotations.

getting rid of illegal states is much more complicated. following a reddit comment, i translated my 4x6 array representation to a list of the 8 corners along with each of their rotations, a number from 0-2. all corners have rotation 0 when the cube is solved. all orderings of the corners are possible, but only if the sum of their rotations mod 3 is equal to 0.

in order to create a reinforcement learning environment, i had to assign rewards based on different states. i tried two different methods:
- 0 reward at all steps, +1 reward when cube is solved
- -1 reward at all steps

both of these worked fine, but when giving +1 reward only when solving the cube, it is important to discount future values by a number more than 0 but less than 1 so that actions leading to faster solutions have higher values than their slower counterparts.

in order to assign values to different states, i used a large dictionary with the states as keys and values as the values. this requires the extra step of converting the array to a bytes object because numpy arrays themselves cannot be used as keys.

from here, applying the algorithm was relatively simple.

# other issues
## slow
my code was very slow.

in particular, my code for turning a face of the cube 90 degrees was very slow.

rather than writing better code, i solved this by creating a second dictionary with keys as an ordered pair of a state and an action, and values as the state which results from taking that action in the key state.

this worked great. it added a few minutes to the start of the training process but significantly sped it up overall.

## neural networks
i wanted to try using a neural network to replace the value function so that it would be possible to use this algorithm when the number of states is too large to iterate through.

because of this, i chose not to use the dictaionary method above to speed up my code, and it was very slow.

additionally, random policies are very bad at solving rubik's cubes, so it very quickly becomes a good strategy for the neural network to either guess that the value of the next state is 0 or $-\infty$ depending on the rewards used, regardless of how good the actual state is.

in order to solve this second issue, i tried training it to only solve cubes which were scrambled by a single move. then, when it had approximated the correct value function, make it solve cubes scrambled by 3 moves, then 5 moves etc. This worked for a while, but slowed down significantly when trying to solve larger numbers of random moves as it was no longer feasable to memorize every possible state.

# next steps
i would like to read more of the second section of *Reinforcement, Learning, an Introduction* to learn more about non tabular reinforcement learning methods.

i would like to apply a more efficient algorithm to solving a 2x2 rubik's cube without needing a full list of states.

if this works, i would like to try the same algorithm on a 3x3 cube, or optimize it further to the point that this is feasible.
