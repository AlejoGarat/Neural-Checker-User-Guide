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
      - [Partial L\*](#partial-l)
      - [Restart L\*](#restart-l)
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
from pythautomata.model_comparators.dfa_comparison_strategy import DFAComparisonStrategy
from pymodelextractor.teachers.general_teacher import GeneralTeacher
from pythautomata.automata_definitions.sample_moore_machines import SampleMooreMachines
from pythautomata.automata_definitions.tomitas_grammars import TomitasGrammars
from pymodelextractor.factories.lstar_factory import LStarFactory
from pythautomata.utilities.nicaud_dfa_generator import generate_dfa
from pythautomata.base_types.alphabet import Alphabet
```

#### Implementation using a Moore machine

```python
# Create the General L* learner that implements the L* algorithm
learner = LStarFactory.get_moore_machine_lstar_learner()

# Import a sample Moore Machine
moore_machine = SampleMooreMachines.get_tomitas_automaton_1()

# Create the teacher for the Moore Machine Learner
mm_teacher = GeneralTeacher(moore_machine, MooreMachineComparisonStrategy())

# Learn the generated Moore Machine
teacher = mm_teacher
result = learner.learn(teacher) 

# Show that the learned Moore Machine is equivalent to the generated Moore Machine
print("Are equal: ", MooreMachineComparisonStrategy().are_equivalent(
    result.model, moore_machine))

# Export the learned Moore Machine
result.model.export('./output_models/mm_lstar/')
```

#### Implementation using a DFA

```python
# Create the General L* learner that implements the L* algorithm
learner = LStarFactory.get_dfa_lstar_learner()

# Import a sample DFA
dfa =  TomitasGrammars.get_automaton_1()

# Create the teacher for the DFA learner
dfa_teacher = GeneralTeacher(dfa, DFAComparisonStrategy())

# Learn the generated DFA
teacher = dfa_teacher
result = learner.learn(teacher) 

# Show that the learned DFA is equivalent to the generated DFA
print("Are equal: ", DFAComparisonStrategy().are_equivalent(
    result.model, dfa))

# Export the learned DFA
result.model.export('./output_models/dfa_lstar/')
```

#### Partial L*

The partial L* algorithm enables the inference of a partial automaton. This is particularly useful when there is a need to constrain the time involved in the learning process.

```python
# Define a maximum time in minutes for the extraction
max_extraction_time = 0.1

# Create the General L* learner that implements the L* algorithm with the partial approach
learner = LStarFactory.get_partial_dfa_lstar_learner(max_time=max_extraction_time)

# Define an input alphabet
input_alphabet = Alphabet.from_strings(["a", "b"])

# Generate a random DFA with 10000 states
dfa = generate_dfa(input_alphabet, 10000, seed=100)

# Create the teacher for the DFA learner
dfa_teacher = GeneralTeacher(dfa, DFAComparisonStrategy())

# Learn the generated DFA
teacher = dfa_teacher
result = learner.learn(teacher) 

# Evaluate if the DFA was learned in the given time
print("Are equal: ", DFAComparisonStrategy().are_equivalent(
    result.model, dfa))

# Export the learned DFA
result.model.export('./output_models/dfa_partial_lstar/')

# Save the Observation Table
observation_table = result.info['observation_table']
```

#### Restart L*

We can use the restart approach to reuse the generated observation table from the last run of the algorithm.

```python
# Learn the generated DFA with the saved Observation Table
result = learner.learn(teacher, observation_table=observation_table) 

# Evaluate if the DFA was learned in the given time
print("Are equal: ", DFAComparisonStrategy().are_equivalent(
    result.model, dfa))
```

## Observation Tree algorithms

### Observation Pack

#### Dependencies

```python
from pymodelextractor.learners.observation_tree_learners.observation_pack_learner import ObservationPackLearner
from pymodelextractor.teachers.automaton_teacher import DeterministicFiniteAutomatonTeacher
from pythautomata.model_comparators.dfa_comparison_strategy import DFAComparisonStrategy
from pythautomata.automata_definitions.tomitas_grammars import TomitasGrammars
```

#### Implementation

```python
# Create the Observation Pack learner that implements the Observation Pack algorithm
learner = ObservationPackLearner()

# Import a sample DFA
dfa = TomitasGrammars.get_automaton_3()

# Create the teacher for the DFA learner
dfa_teacher = DeterministicFiniteAutomatonTeacher(dfa, DFAComparisonStrategy())

# Learn the generated DFA
teacher = dfa_teacher
result = learner.learn(teacher)

# Show that the learned DFA is equivalent to the generated DFA
print("Are equal: ", DFAComparisonStrategy().are_equivalent(
    result.model, dfa))

# Export the learned DFA
result.model.export('./output_models/dfa_observation_pack/')
```

### Kearns and Vazirani

#### Dependencies

```python
from pymodelextractor.learners.observation_tree_learners.kearns_vazirani_learner import KearnsVaziraniLearner
from pymodelextractor.teachers.automaton_teacher import DeterministicFiniteAutomatonTeacher
from pythautomata.model_comparators.dfa_comparison_strategy import DFAComparisonStrategy
from pythautomata.automata_definitions.tomitas_grammars import TomitasGrammars
```

#### Implementation

```python
# Create the Observation Pack learner that implements the Kearns and Vazirani algorithm
learner = KearnsVaziraniLearner()

# Import a sample DFA
dfa = TomitasGrammars.get_automaton_4()

# Create the teacher for the DFA learner
dfa_teacher = DeterministicFiniteAutomatonTeacher(dfa, DFAComparisonStrategy())

# Learn the generated DFA
teacher = dfa_teacher
result = learner.learn(teacher)

# Show that the learned DFA is equivalent to the generated DFA
print("Are equal: ", DFAComparisonStrategy().are_equivalent(
    result.model, dfa))

# Export the learned DFA
result.model.export('./output_models/dfa_kearns_vazirani/')
```
