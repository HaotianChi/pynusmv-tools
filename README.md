# PyNuSMV-Tools
This package contains several tools and experiments that have been developed using
PyNuSMV. These tools serve as examples of how to use PyNuSMV and what can be
achieved.

## About PyNuSMV
PyNuSMV is a Python binding for NuSMV developed by the Louvain Verification Lab.
It is intended to provide a Python interface to NuSMV, allowing to use NuSMV as
a library. (And using it is exactly what is done in the pynusmv-tools package !)

More details about this tool are provided in the PyNuSMV documentation
(see the DOCUMENTATION section below).

## Installation
Give some instructions for the installation. Should revolve around a plain
````
pip3 install pynusmv-tools
````

## Contents

### Tree-Like Annotated Counter-Examples
This tool is provided in the `pynusmv_tools.tlace` package.

Tree-Like Annotated Counter-Examples (TLACEs for short) are rich branching counter-examples [BP12]. This tool extends NuSMV explanations to produce these rich counter-examples instead of single paths. It uses NuSMV model checking algorithms and path explanation generators (ex_explain, eg_explain, eu_explain), the parser for CTL formulas, etc. The result is a tool taking an SMV model as argument, checking all CTL properties of the model and producing TLACEs when the property is violated.

To run this tool, launch the following command from the src/ directory of PyNuSMV package.
````
tlace MODEL
````
where MODEL is an SMV model. The tool computes the given model, check all its CTL specifications and produces a TLACE explaining why each violated specification is violated.


### ARCTL with TLACE generation
Action-Restricted CTL (ARCTL for short) is an extension of CTL where quantified paths are restricted to paths of actions satisfying a propositional property [PR07]. This tool is defined in the `pynusmv_tools.arctl` package.

It performs ARCTL model checking on SMV models and produces counter-examples and witnesses to explain ARCTL properties violation or satisfaction. These diagnostics can be either simple paths of the system or full TLACEs; TLACEs are defined in the separated sub-package `pynusmv_tools.arctl.tlace`.

Finally, this tool provides two CLI to use these functionalities; these CLI are defined in the sub-package `pynusmv_tools.arctl.cmd`. `pynusmv_tools.arctl.cmd.trace` is a CLI that allows to read a system from an SMV model and to check ARCTL specifications over it. Whatever the result of the verification, this CLI produces a diagnostic to explain the verification outcome. This CLI produces simple paths of the system as diagnostics. On the other hand, `pynusmv_tools.arctl.cmd.tlace` provides the same functionalities but produces TLACEs as diagnostics.

To run this tool, launch the following command:
````
arctl
````

This gives a prompt accepting some commands. The list of accepted commands can be shown using the help command.

To launch the CLI producing TLACEs. Just run the same command with tlace instead of trace; accepted commands are the same.


### CTLK model checking and TLACE generation and exploration
CTLK is an extension of CTLK allowing to reason about time and knowledge of the agents of a system [PL03]. This logic is interpreted over systems composed of agents that have a limited knowledge of the system current state. It allows to mix temporal and epistemic operators to express, for example, that at all time, an agent knows that a property is true.

This tool performs model checking of CTLK on multi-agent systems defined with the SMV language. Il also generates diagnostics for explaining verification outcomes. Finally, it allows the user to interactively explore a TLACE explaining the result of verification.

More precisely, this tool accepts any SMV model and follows these conventions:
    - any instance of a module declared in the "main" module is considered an agent;
    - the direct knowledge of an agent is given by all its local variables, i.e. the variables declared in its module, and by all its arguments that are variables.

To run this tool, launch the following command:
````
ctlk
````

This gives a prompt accepting some commands listed by the "help" command. This prompt accepts, for example, the "model" command to specify an SMV model, the "check" command to verify a CTLK property, or the "explain" command to explain the model checking result. In particular, the "explain" command opens another prompt to explore the diagnostic; the help can be accessed through the "help" command. Finally, note that any command has its own help message, accessible through the "help <cmd>" command.


### CTL model checking
PyNuSMV is shipped with a reimplementation of CTL model checking, for performance measures. This tool can be run with
````
ctl [model]
````
where [model] is the path to a NuSMV model. The tool loads the model, checks every CTL specification in the model file and outputs its truth value.


### Fair CTL model checking
A reimplementation of Fair CTL model checking is provided. This tool can be run with
````
fairctl [model]
````
where model is the path to a NuSMV model. The tool loads the model, checks every CTL specification in the model file (under fairness constraints of the model) and outputs its truth value.


### Multi-agent systems
An implementation of Multi-agent systems (MAS) is provided with PyNuSMV. The tool is a library to define and manipulate MASs with NuSMV. To load a model and get the corresponding MAS, you have to use `pynusmv_tools.mas.glob` to load a model (`load_from_file` function) and to get the MAS (mas function).

On a MAS, you can perform any action given by a PyNuSMV BddFsm, plus additional functionalities like getting equivalent states according to a set of agents' knowledge or getting the protocol of any subset of agents (see `pynusmv_tools.mas.mas` for more information).

The assumptions on the given SMV model representing a MAS are the following:
 - Every module instance of the MAIN module is considered as an agent;
 - The agent's name is the variable name of the instance;
 - The knowledge of the agent (its observations) are its internal state variables (the variables beginning with agent's name) and all variables given as a parameter at instanciation of the module (only pure variables are considered, not expressions or input variables).


### ATL model checking
An implementation of ATL model checking is provided, relying on the MAS library. This tool can be run with
````
atl [model]
````
where model is the path to an SMV model representing a Multi-agent system (see above). The tool loads the model and starts a minimal command-line interface. Every typed ATL formula is checked and the result of the verification is given at standard output. The command-line interface can be exited with EOF (CTRL-D).

The grammar for ATL properties phi is the following:
````
  phi ::= EXPR | '(' phi ')' | phi '&' phi | phi '|' phi | '~'phi | phi '->' phi
          | phi '<->' phi | '<' group '>' psi | '[' GROUP ']' psi
  psi ::= 'X' phi | 'G' phi | 'F' phi | '[' phi 'U' phi ']' | '[' phi 'W' phi ']'
````
where EXPR is any simple expression over variables of the system, enclosed into simple quotes (e.g. EXPR = 'transmission = completed' or EXPR = 'v = 3'), and GROUP is a list of agent names enclosed into simple quotes, separated by commas (e.g. GROUP = 'sender', 'transmitter' or GROUP = 'dealer').


### Model checking ATLK with full and partial observability
An implementation of model checking algorithms for ATLK with full and partial observability [BPQR13] is provided. This tool can be run with
````
atlk_fo [model]
````
for the full observability setting, and with
````
atlk_po [model]
````
for the partial observability setting.

[model] is the path to an SMV model representing a Multi-agent system (see above). The tool loads the model and starts a minimal command-line interface. Every typed ATLK formula is checked and the result of the verification is given at standard output. The command-line interface can be exited with EOF (CTRL-D).

The grammar for ATLK properties phi (under both semantics) is the following:
````
  phi ::= EXPR | '(' phi ')' | phi '&' phi | phi '|' phi | '~'phi | phi '->' phi
          | phi '<->' phi | '<' group '>' psi | '[' GROUP ']' psi
          | 'A' psi | 'E' psi | 'nK' '<' AGENT '>' phi | 'K' '<' AGENT '>' phi
          | 'nE' '<' GROUP '>' phi | 'K' '<' GROUP '>' phi
          | 'nD' '<' GROUP '>' phi | 'D' '<' GROUP '>' phi
          | 'nC' '<' GROUP '>' phi | 'C' '<' GROUP '>' phi
  psi ::= 'X' phi | 'G' phi | 'F' phi | '[' phi 'U' phi ']' | '[' phi 'W' phi ']'
````
where EXPR is any simple expression over variables of the system, enclosed into simple quotes (e.g. `EXPR = 'transmission = completed'` or `EXPR = 'v = 3'`), AGENT is an agent name enclosed into simple quotes, and GROUP is a comma-separated list of agent names (e.g. GROUP = 'sender', 'transmitter' or AGENT = 'dealer').


A second implementation of the algorithms can be run with
````
atlk_irf -m [model] -p [property]
````

[property] is as above, but [model] can be either a model as above, or a python module containing the `model()` function, that returns the SMV model itself. If the module contains a `agents()` function, it is used to retreive the list of agents of the model (instead of infering them as explained above).

The tool supports several command-line arguments. For their list and descriptions, use
````
atlk_irf --help
````


### A framework for mu-calculus based logic explanations
An implementation of a framework for mu-calculus based logic explanations is provided. It relies on the fact that many branching logics such as CTL, ATL, ARCTL, etc. can be translated into the mu-calculus. The framework lies in the `pynusmv_tools.mucalculus` package and provides
 - a mu-calculus model checker that produces rich explanations
 - a set of functionalities to translate these rich explanations back into the original logic
 - a graphical interface based on tkinter to display and manipulate the translated explanations.


### DOT model export
A DOT model export tool is provided. To run this tool, run
````
smv2dot model
````
where model is the path to an SMV model. The output of the tool is a DOT representation of the reachable state-space of the model, printed on standard output (copy/paste the output in a file or redirect sdtout into a file). This is useful to get an overview of the state-space and to inspect more precisely the model in a visual way.


### Models comparator
A tool to compare to NuSMV models is provided. To run this tool, run
````
smv_cmp first second
````
where first and second are paths to two SMV models. The output of the tool is a
comparison of both models in terms of states and transitions. The tool is
useful to compare two similar models, that is, models based on the same
set of variables but with different reachable state-space and transition
relations. The tool can, in this case, show states that are in one model but
not in another, as well as transitions that appear in one model but not in the
other.

## Bounded Model checking
### Diagnosability
This tool adopts the approach of [PCC02] and performs a bounded diagnosability
test of a system represented as an SMV model. To run the tool, just use the
`diagnos` command. Complete usage information is available using

````
diagnos --help
````

#### More information
More information and details about this tool can be found in sections 4.3 of
[GP16]http://dial.uclouvain.be/downloader/downloader.php?pid=thesis%3A4575&datastream=PDF_01

### BMC LTL
The LTL bounded model checking verifier provided in this package comes in three
flavours. The first one `bmc_ltl` was developed to illustrate the way one could
use the NuSMV api exposed throught pynusmv to easily carry out sat based bounded
model checking. This tool uses the NuSMV syntax for LTL properties.

The second alternative `bmc_ltl_li` pursues the same objective but illustrates
in greater depth how one can use the bmc related capabilities of pynusmv to
write verification tools. (Uses less high level apis). This tool uses the NuSMV
syntax for LTL properties.

The third one `bmc_ltl_py` illustrates how easily one can use the bmc extension
of pynusmv to create tools that verify custom logics. This tool uses a different
syntax from the standard SMV one.

#### Syntax of bmc_ltl_py
````
LTL         :=   '!'  LTL
             |   '[]' LTL
             |   '<>' LTL
             |   '()' LTL
             |   LTL 'U'   LTL
             |   LTL 'W'   LTL
             |   LTL '&'   LTL
             |   LTL '|'   LTL
             |   LTL '^'   LTL
             |   LTL '=>'  LTL
             |   LTL '<=>' LTL
             |   comparison


comparison  :=   arith '<'  arith
             |   arith '<=' arith
             |   arith '>'  arith
             |   arith '>=' arith
             |   arith '='  arith
             |   arith '!=' arith
             |   arith

arith       := - arith
             |   arith '<<'  arith
             |   arith '>>'  arith
             |   arith '*'   arith
             |   arith '/'   arith
             |   arith 'mod' arith
             |   arith '+'   arith
             |   arith '-'   arith
             |   atom

atom        := TRUE | FALSE | variable | number

variable    := [a-zA-Z_@]+[a-zA-Z0-9_@.]*
number      := [0-9]+
````

#### More information
More information and details about these tools can be found in sections 4.1-4.2 of
[GP16]http://dial.uclouvain.be/downloader/downloader.php?pid=thesis%3A4575&datastream=PDF_01


## REFERENCES
* [BP12] Simon Busard, Charles Pecheur: Rich Counter-Examples for Temporal-Epistemic Logic Model Checking. IWIGP 2012: 39-53
* [BPQR13] Simon Busard, Charles Pecheur, Hongyang Qu, Franco Raimondi: Reasoning about Strategies under Partial Observability and Fairness Constraints. Proceedings of SR 2013, EPTCS Vol. 112, pp. 71–79, 2013.
* [PL03] W.Penczek, A.Lomuscio: Verifying epistemic properties of multi-agent systems via bounded model checking. Fundamenta Informaticae, 55(2) :167–185, 2003.
* [PR07] Charles Pecheur, Franco Raimondi: Symbolic Model Checking of Logics with Actions. MoChArt 2006: 113-128
* [GP16] Xavier Gillard: Adding SAT-based model checking to the PyNuSMV framework. ([MSc Thesis]http://dial.uclouvain.be/downloader/downloader.php?pid=thesis%3A4575&datastream=PDF_01)
* [PCC02] Charles Pecheur, Alessandro Cimatti, Roberto Cavada: Formal verification of diagnosability via symbolic model checking. Workshop on Model Checking and Artificial Intelligence (MoChArt-2002), Lyon, France. 2002.
* [BCZ99] Armin Biere, Alessandro Cimatti, Edmund Clarke, Yunshan Zhu: Symbolic model checking without BDDs. In International conference on tools and algorithms for the construction and analysis of systems (pp. 193-207). Springer Berlin Heidelberg.
* [BCCSZ03] Armin Biere, Alessandro Cimatti, Edmund Clarke, Ofer Strichman, Yunshan Zhu, Y. (2003). Bounded model checking. Advances in computers, 58, 117-148.

## Credits
PyNuSMV is developed, maintained and distributed by the LVL Group at Université
Catholique de Louvain. Please contact <lvl at listes dot uclouvain dot be> for any
question regarding this software distribution.

NuSMV is a symbolic model checker developed as a joint project between several
partners and distributed under the GNU LGPL license. Please contact <nusmv at
fbk dot eu> for getting in touch with the NuSMV development staff.
