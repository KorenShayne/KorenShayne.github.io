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


