# Pythautomata user guide <!-- TOC ignore:true -->

The aim of this document is to guide the user in the usage of the Pythautomata python library used for modeling finite state systems.

<!-- TOC -->
- [Pythautomata user guide ](#pythautomata-user-guide-)
  - [Install pythautomata](#install-pythautomata)
  - [Moore machines](#moore-machines)
    - [Dependencies](#dependencies)
    - [Implementation](#implementation)
  - [Mealy machines](#mealy-machines)
    - [Dependencies](#dependencies-1)
    - [Implementation](#implementation-1)
  - [Generators](#generators)
    - [Moore machine generator](#moore-machine-generator)
      - [Dependencies](#dependencies-2)
      - [Implementation](#implementation-2)
  - [Converters and Comparators](#converters-and-comparators)
    - [DFA - Moore machine converter and comparator](#dfa---moore-machine-converter-and-comparator)
      - [Dependencies](#dependencies-3)
      - [Implementation](#implementation-3)
    - [Moore machine - Mealy machine converter and comparator](#moore-machine---mealy-machine-converter-and-comparator)
      - [Dependencies](#dependencies-4)
      - [Implementation](#implementation-4)

<!-- /TOC -->

## Install pythautomata

```python
%pip install pythautomata 
```

## Moore machines

    This definition of moore machines is based on:
        Author, Georgios Giantamidis, Author, Stavros Tripakis (2016). 
            Learning Moore Machines from Input-Output Traces
            https://arxiv.org/pdf/1605.07805.pdf


### Dependencies 

```python
from pythautomata.base_types.alphabet import Alphabet
from pythautomata.base_types.moore_state import MooreState
from pythautomata.base_types.symbol import SymbolStr
from pythautomata.automata.moore_machine_automaton import MooreMachineAutomaton
from pythautomata.model_comparators.moore_machine_comparison_strategy import MooreMachineComparisonStrategy as ComparisonStrategy
from pythautomata.model_exporters.dot_exporters.moore_dot_exporting_strategy import MooreDotExportingStrategy as DotExportingMMStrategy
```

### Implementation

```python
# First we create an input alphabet with symbols a, b and c
input_alphabet = Alphabet(frozenset((SymbolStr('a'), SymbolStr('b'), SymbolStr('c'))))
# Then the output alphabet with symbols 0 and 1
output_alphabet = Alphabet(frozenset((SymbolStr('0'), SymbolStr('1'))))

a = input_alphabet['a']
b = input_alphabet['b']
c = input_alphabet['c']

# Defining moore states
stateA = MooreState(name="State A", value=output_alphabet['0'])
stateB = MooreState("State B", output_alphabet['1'])
stateC = MooreState("State C", output_alphabet['1'])

# Adding transitions for the previous states
stateA.add_transition(a, stateA)
stateA.add_transition(b, stateB)
stateB.add_transition(a, stateC)
stateB.add_transition(c, stateB)

# Finaly we create the moore machine with the alphabets and states previously created 
moore_machine = MooreMachineAutomaton(input_alphabet, output_alphabet, initial_state=stateA, 
                                      states=set([stateA, stateB, stateC]), comparator=ComparisonStrategy, 
                                      name="moore machine with 3 states")

# Moore machine is exported in the directory ./output_models/moore_machines/ with .dot extension
moore_machine.export('./output_models/moore_machines/')
```

## Mealy machines

### Dependencies 

```python
from pythautomata.base_types.alphabet import Alphabet
from pythautomata.base_types.mealy_state import MealyState
from pythautomata.base_types.symbol import SymbolStr
from pythautomata.automata.mealy_machine import MealyMachine
from pythautomata.model_comparators.mealy_machine_comparison_strategy import MealyMachineComparisonStrategy as MealyComparisonStrategy
```

### Implementation

```python
# First we create an input alphabet with symbols a, b and c
input_alphabet = Alphabet(frozenset((SymbolStr('a'), SymbolStr('b'), SymbolStr('c'))))
# Then the output alphabet with symbols 0 and 1
output_alphabet = Alphabet(frozenset((SymbolStr('0'), SymbolStr('1'))))

a = input_alphabet['a']
b = input_alphabet['b']
c = input_alphabet['c']

zero = output_alphabet['0']
one = output_alphabet['1']

# Defining mealy states
stateA = MealyState(name="State A")
stateB = MealyState(name="State B")
stateC = MealyState(name="State C")

# Adding transitions for the previous states
stateA.add_transition(a, stateA, zero)
stateA.add_transition(b, stateB, one)
stateB.add_transition(a, stateC, zero)
stateB.add_transition(c, stateB, one)

# Finaly we create the mealy machine with the alphabets and states previously created 
mealy_machine = MealyMachine(input_alphabet, output_alphabet, initial_state=stateA, states=set([stateA, stateB, stateC]), 
                             comparator=MealyComparisonStrategy, 
                             name="mealy machine with 3 states")

# Mealy machine is exported in the directory ./output_models/mealy_machines/ with .dot extension
mealy_machine.export('./output_models/mealy_machines/')
```

## Generators

### Moore machine generator

#### Dependencies 

```python
from pythautomata.utilities.nicaud_mm_generator import generate_moore_machine
```

#### Implementation

```python
# Generate a random Moore Machine with 500 states
input_alphabet = Alphabet.from_strings(["a", "b", "c"])
output_alphabet = Alphabet.from_strings(['0', '1'])
seed = 100
number_of_states = 500
automata = generate_moore_machine(input_alphabet, output_alphabet, number_of_states, seed)
```

## Converters and Comparators

### DFA - Moore machine converter and comparator

#### Dependencies 


```python
from pythautomata.utilities.automata_converter import AutomataConverter
from pythautomata.automata_definitions.tomitas_grammars import TomitasGrammars
from pythautomata.automata_definitions.sample_moore_machines import SampleMooreMachines
from pythautomata.model_comparators.moore_machine_comparison_strategy import MooreMachineComparisonStrategy
```

#### Implementation

```python
# Tomitas DFA example
tomitas = TomitasGrammars.get_automaton_1()

# Convert tomitas DFA to moore machine
converted_moore_machine = AutomataConverter.convert_dfa_to_moore_machine(tomitas)

# Manually created tomitas moore machine
tomitas_moore_machine = SampleMooreMachines.get_tomitas_automaton_1()

# Comparate tomitas automatons
comparison_strategy = MooreMachineComparisonStrategy()
comparison_strategy.are_equivalent(automaton1=converted_moore_machine, automaton2=tomitas_moore_machine)
```

### Moore machine - Mealy machine converter and comparator

#### Dependencies 


```python
from pythautomata.utilities.automata_converter import AutomataConverter
from pythautomata.automata_definitions.sample_moore_machines import SampleMooreMachines
from pythautomata.model_comparators.mealy_machine_comparison_strategy import MealyMachineComparisonStrategy
```

#### Implementation

```python
# Tomitas DFA example
moore_machine = SampleMooreMachines.get_3_states_automaton()

# Convert tomitas DFA to moore machine
converted_mealy_machine = AutomataConverter.convert_moore_machine_to_mealy_machine(moore_machine)

# Manually created mealy machine
input_alphabet = Alphabet(frozenset((SymbolStr('a'), SymbolStr('b'))))
output_alphabet = Alphabet(frozenset((SymbolStr('a'), SymbolStr('b'), SymbolStr('c'))))

a = input_alphabet['a']
b = input_alphabet['b']

output_a = output_alphabet['a']
output_b = output_alphabet['b']
output_c = output_alphabet['c']

# Defining mealy states
stateA = MealyState(name="State A")
stateB = MealyState(name="State B")
stateC = MealyState(name="State C")

# Adding transitions for the previous states
stateA.add_transition(a, stateB, output_b)
stateA.add_transition(b, stateC, output_c)
stateB.add_transition(a, stateC, output_c)
stateB.add_transition(b, stateA, output_a)
stateC.add_transition(a, stateA, output_a)
stateC.add_transition(b, stateB, output_b)

# Finaly we create the mealy machine with the alphabets and states previously created 
mealy_machine = MealyMachine(input_alphabet, output_alphabet, initial_state=stateA, states=set([stateA, stateB, stateC]), 
                             comparator=MealyComparisonStrategy, 
                             name="mealy machine with 3 states")

# Comparate automatons
comparison_strategy = MealyMachineComparisonStrategy()
comparison_strategy.are_equivalent(machine1=converted_mealy_machine, machine2=mealy_machine)
```