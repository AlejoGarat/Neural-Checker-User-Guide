# PyModelExtractor <!-- TOC ignore:true -->

The aim of this document is to guide the user in the usage of the pyModelExtractor library which is a Python framework for the active learning of Automata in a black box context.

<!-- TOC -->

- [PyModelExtractor ](#pymodelextractor-)
  - [Installation](#installation)
  - [Observation Table algorithms](#observation-table-algorithms)
    - [General L\*](#general-l)
      - [Dependencies](#dependencies)
      - [Implementation using a Moore machine](#implementation-using-a-moore-machine)
      - [Implementation using a DFA](#implementation-using-a-dfa)
  - [Observation Tree algorithms](#observation-tree-algorithms)
    - [Observation Pack](#observation-pack)
      - [Dependencies](#dependencies-1)
      - [Implementation](#implementation)
    - [Kearns and Vazirani](#kearns-and-vazirani)
      - [Dependencies](#dependencies-2)
      - [Implementation](#implementation-1)

<!-- /TOC -->

## Installation

```python
%pip install pymodelextractor
```

## Observation Table algorithms

### General L*

#### Dependencies

```python
from pythautomata.model_comparators.moore_machine_comparison_strategy import MooreMachineComparisonStrategy
from pymodelextractor.learners.observation_table_learners.mm_lstar_learner import MMLStarLearner as MooreMachineLearner
from pymodelextractor.teachers.moore_machines_teacher import MooreMachineTeacher as MMTeacher
from pythautomata.automata_definitions.sample_moore_machines import SampleMooreMachines
```

#### Implementation using a Moore machine

```python
# Create the Moore Machine Learner that implements the L* algorithm
learner = MooreMachineLearner()

# Import a sample Moore Machine
moore_machine = SampleMooreMachines.get_tomitas_automaton_1()

# Create the teacher for the Moore Machine Learner
mm_teacher = MMTeacher(moore_machine, MooreMachineComparisonStrategy())

# Learn the generated Moore Machine
teacher = mm_teacher
result = learner.learn(teacher, verbose=True) 
# verbose=True prints the learning process, the default value is False


# Show that the learned Moore Machine is equivalent to the generated Moore Machine
print("Are equal: ", MooreMachineComparisonStrategy().are_equivalent(
    result.model, moore_machine))

# Export the learned Moore Machine
result.model.export('./output_models/mm_lstar/')
```

#### Implementation using a DFA

## Observation Tree algorithms

### Observation Pack

#### Dependencies

#### Implementation

### Kearns and Vazirani

#### Dependencies

#### Implementation