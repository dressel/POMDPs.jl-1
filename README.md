# POMDPs

This package provides a basic interface for working with partially observable Markov decision processes (POMDPs).

The goal is to provide a common programming vocabulary for researchers and students to use primarily for three tasks:

1. Expressing problems using the POMDP format. 
2. Writing solver software.
3. Running simulations efficiently.


## Installation
```julia
Pkg.clone("https://github.com/sisl/POMDPs.jl.git")
```

## Basic Example

**TODO**

## Supported Solvers

**TODO**: Update these solvers after interface changes

The following MDP solvers support this interface:
* [Value Iteration](https://github.com/sisl/DiscreteValueIteration.jl)
* [Monte Carlo Tree Search](https://github.com/sisl/MCTS.jl)

The following POMDP solvers support this interface:
* [QMDP](https://github.com/sisl/QMDP.jl)
* [SARSOP](https://github.com/sisl/SARSOP.jl)


## Core Interface

The core interface provides tools to express problems, program solvers, and setup simulations.

### Distributions

`AbstractDistribution`

- `rand!(rng::AbstractRNG, sample, d::AbstractDistribution)` fill with random sample from distribution
- `pdf(d::AbstractDistribution, x)` value of probability distribution function at x

### Problem Model

`POMDP`
`AbstractSpace`
`State`
`Action`
`Observation`

- `states(pomdp::POMDP)` returns the complete state space 
- `actions(pomdp::POMDP)` returns the complete action space
- `actions(pomdp::POMDP, state::State, aspace::AbstractSpace=actions(pomdp))` modifies `aspace` to the action space accessible from the given state and returns it
- `observations(pomdp::POMDP)` returns the complete observation space
- `observations(pomdp::POMDP, state::State, ospace::AbstractSpace)` modifies `ospace` to the observation space accessible from the given state and returns it
- `reward(pomdp::POMDP, state::State, action::Action)` returns the immediate reward for the state-action pair
- `reward(pomdp::POMDP, state::State, action::Action, statep::State)` returns the immediate reward for the s-a-s' triple
- `transition(pomdp::POMDP, state::State, action::Action, distribution=create_transition_distribution(pomdp))` modifies `distribution` to the transition distribution from the current state-action pair and returns it
- `observation(pomdp::POMDP, state::State, action::Action, distribution=create_observation_distribution(pomdp))` modifies `distribution` to the observation distribution from the current state and *previous* action and returns it
- `discount(pomdp::POMDP)` returns the discount factor
- `isterminal(pomdp::POMDP, state::State)` checks if a state is terminal

### Solvers and Polices

`Solver`
`Policy`

- `solve(solver::Solver, pomdp::POMDP, policy::Policy=create_policy(solver, pomdp))` solves the POMDP and modifies `policy` to be the solution of `pomdp` and returns it
- `action(pomdp::POMDP, policy::Policy, belief::Belief, action=create_action(pomdp))` returns an action for the current belief given the policy
- `action(pomdp::POMDP, policy::Policy, state::State, action=create_action(pomdp))` returns an action for the current state given the policy

### Belief

`Belief`
`BeliefUpdater`

- `update(updater::BeliefUpdater, belief_old::Belief, action::Action, obs::Observation, belief_new::Belief=create_belief(updater))` modifies `belief_new` to the belief given the old belief and the latest action and observation and returns the updated belief. 

<!-- `belief_old` and `belief_new` should *not* be references to the same object
-->

### Simulation

`Simulator`

- `simulate(simulator::Simulator, pomdp::POMDP, policy::Policy, updater::BeliefUpdater, initial_belief::Belief)` runs a simulation using the specified policy and returns the accumulated reward

## Minor Components

### Convenience Functions

Several convenience functions are also provided in the interface to provide standard vocabulary for common tasks and may be used by some solvers or in simulation, but they are not strictly necessary for expressing problems.

- `index(pomdp::POMDP, state::State)` returns the index of the given state for a discrete POMDP 
- `initial_belief(pomdp::POMDP)` returns an example initial belief for the pomdp
- `domain(space::AbstractSpace)` returns an iterator over a space
- `value(policy::Policy, belief::Belief)` returns the expected value for the current belief given the policy
- `value(policy::Policy, state::State)` returns the expected value for the current state given the policy
- `convert_belief(updater::BeliefUpdater, b::Belief)` returns a belief that can be updated using `updater` that has a similar distribution to `b` (this conversion may be lossy)
- `updater(p::Policy)` returns a default BeliefUpdater appropriate for a belief type that policy `p` can use

### Object Creators

In many cases, it is more efficient to fill pre-allocated objects with new data rather than create new objects at each iteration of an algorithm or simulation. When a new object is needed, the following functions may be called. They should return an object of the appropriate type as efficiently as possible. The data in the object does not matter - it will be overwritten when the object is used.

- `create_state(pomdp::POMDP)` creates a single state object (for preallocation purposes)
- `create_observation(pomdp::POMDP)` creates a single observation object (for preallocation purposes)
- `create_transition_distribution(pomdp::POMDP)` returns a transition distribution
- `create_observation_distribution(pomdp::POMDP)` returns an observation distribution
- `create_policy(solver::Solver, pomdp::POMDP)` creates a policy object (for preallocation purposes)
- `create_action(pomdp::POMDP)` returns an action (for preallocation purposes)
- `create_belief(updater::BeliefUpdater)` creates a belief object (for preallocation purposes)

## Reference Simulation Implementation

This reference simulation implementation shows how the various functions will be used.

```julia
type ReferenceSimulator
    rng::AbstractRNG
    max_steps
end

function simulate(simulator::ReferenceSimulator, pomdp::POMDP, policy::Policy, updater::BeliefUpdater, initial_belief::Belief)

# TODO: fill this in

end

```
