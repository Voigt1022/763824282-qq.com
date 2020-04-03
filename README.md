# Knowledge Base: Rules

In this lab assignment, you are going to extend a knowledge base (KB) and an inference engine. The knowledge base supports three main interfaces: `Assert`, `Retract`, and `Ask`.

- `Assert`: Add facts or rules into the knowledge base. After you add facts or rules into the KB, the forward-chaining algorithm is used to infer other facts or rules.
- `Ask`: ask queries and return a list of bindings for facts.
- `Retract`: remove facts from the knowledge base. Also, remove all other facts or rules that are dependent on the removed fact or rule.

## File Breakdown

Below is a description of each included file and the classes contained within each including a listing of their attributes. Each file has documentation in the code reflecting the information below (in most cases they are exactly the same). As you read through the attributes follow along in the corresponding files and make sure you're understanding the descriptions.

Attributes of each class are listed in the following format (_Note:_ if you see a type like `Fact|Rule` the `|` type is `or` and mean the type can be either Fact or Rule):

- `field_name` (`type`) - text description

### logical_classes.py

This file defines all basic structure classes.

#### Fact

Represents a fact in our knowledge base. Has a statement containing the content of the fact, e.g. (isa Sorceress Wizard) and fields tracking which facts/rules in the KB it supports and is supported by.

**Attributes**

- `name` (`str`): 'fact', the name of this class
- `statement` (`Statement`): statement of this fact, basically what the fact actually says
- `asserted` (`bool`): flag indicating if fact was asserted instead of inferred from other rules in the KB
- `supported_by` (`listof Fact|Rule`): Facts/Rules that allow inference of the statement
- `supports_facts` (`listof Fact`): Facts that this fact supports
- `supports_rules` (`listof Rule`): Rules that this fact supports

#### Rule

Represents a rule in our knowledge base. Has a list of statements (the LHS) containing the statements that need to be in our KB for us to infer the RHS statement. Also has fields tracking which facts/rules in the KB it supports and is supported by.

**Attributes**

- `name` (`str`): 'rule', the name of this class
- `lhs` (`listof Statement`): LHS statements of this rule
- `rhs` (`Statement`): RHS statment of this rule
- `asserted` (`bool`): flag indicating if rule was asserted instead of inferred from other rules/facts in the KB
- `supported_by` (`listof Fact|Rule`): Facts/Rules that allow inference of the statement
- `supports_facts` (`listof Fact`): Facts that this rule supports
- `supports_rules` (`listof Rule`): Rules that this rule supports

#### Statement

Represents a statement in our knowledge base, e.g. (attacked Ai Nosliw), (diamonds Loot), (isa Sorceress Wizard), etc. These statements show up in Facts or on the LHS and RHS of Rules.

**Attributes**

- `predicate` (`str`) - the predicate of the statement, e.g. isa, hero, needs
- `terms` (`listof Term`) - list of terms (Variable or Constant) in the statement, e.g. `'Nosliw'` or `'?d'`

#### Term

Represents a term (a Variable or Constant) in our knowledge base. Can sorta be thought of as a super class of Variable and Constant, though there is no actual inheritance implemented in the code.

**Attributes**

- `term` (`Variable|Constant`) - The Variable or Constant that this term holds (represents)

#### Variable

Represents a variable used in statements, e.g. `?x`.

**Attributes**

- `element` (`str`): The name of the variable, e.g. `'?x'`

#### Constant

Represents a constant used in statements

**Attributes**

- `element` (`str`): The value of the constant, e.g. `'Nosliw'`

#### Binding

Represents a binding of a constant to a variable, e.g. `'Nosliw'` might be bound to `'?d'`

**Attributes**

- `variable` (`str`): The name of the variable associated with this binding, e.g. `'?d'`
- `constant` (`str`): The value of the variable, e.g. `'Nosliw'`

#### Bindings

Represents Binding(s) used while matching two statements

**Attributes**

- `bindings` (`listof Bindings`) - bindings involved in match
- `bindings_dict` (`dictof Bindings`) - bindings involved in match where key is bound variable and value is bound value, e.g. some_bindings.bindings_dict['?d'] => 'Nosliw'

**Methods**

- `add_binding(variable, value)` (`(Variable, Constant) => void`) - add a binding from a variable to a value
- `bound_to(variable)` (`(Variable) => Variable|Constant|False`) - check if variable is bound. If so return value bound to it, else False
- `test_and_bind(variable_verm,value_term)` (`(Term, Term) => bool`) - Check if variable_term already bound. If so return whether or not passed in value_term matches bound value. If not, add binding between variable_terma and value_term and return True.

#### ListOfBindings

Container for multiple Bindings

**Methods**

- `add_bindings(bindings, facts_rules)` - (`(Bindings, listof Fact|Rule) => void`) - add given bindings to list of Bindings along with associated rules or facts

### read.py

This file has no classes but defines useful helper functions for reading input from the user or a file.

**Functions**

- `read_tokenize(file)` - (`(str) => (listof Fact, listof Rule)`) - takes a filename, reads the file and returns a fact list and rule list.
- `read_from_input(message)` - (`(str) => str`) - collects user input from the command line.
- `parse_input(e)` - (`(str) => (int, str | listof str)`) - parses input, cleaning it as it does and assigning labels
- `get_new_fact_or_rule()` - (`() => Fact | Rule`) - get a new fact or rule by typing, nothing passed in, data comes from user input
- `get_new_statements()` - (`() => listof Statement`) - read statements from input, nothing passed in, data comes from user input

### util.py

This file has no classes but defines useful helper functions.

**Functions**

- `is_var(var)` (`(str|Variable|Constant|Term) => bool`) - check whether an element is a variable (either instance of Variable or string starting with `'?'`, e.g. `'?d'`)
- `match(state1, state2, bindings=None)` (`(Statement, Statement, Bindings) => Bindings|False`) - match two statements and return the associated bindings or False if there is no binding
- `match_recursive(terms1, terms2, bindings)` (`(listof Term, listof Term, Bindings) => Bindings|False`) - recursive helper for match
- `instantiate(statement, bindings)` (`(Statement, Bindings) => Statement|Term`)  - generate Statement from given statement and bindings. Constructed statement has bound values for variables if they exist in bindings.
- `vprint(message, level, verbose, data=[])` (`(str, int, int, listof any) => void`) - prints message if verbose > level, if data provided then formats message with given data

### core_code.py

This file defines the two classes, KnowledgeBase and InferenceEngine.

#### KnowledgeBase

Represents a knowledge base and implements the three actions described in the writeup (`Assert`, `Retract` and `Ask`)

#### InferenceEngine

Represents an inference engine. Implements forward-chaining in this lab.
