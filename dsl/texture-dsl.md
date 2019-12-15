# Textual Domain Specific Languages

MontiCore: A Framework for the Development of Textual Domain Specific Languages

https://www.se-rwth.de/staff/rumpe/publications20042008/MontiCore-A-Framework-for-the-Development-of-Textual-Domain-Specific-Languages.pdf

```
grammar Automaton {

Automaton =
  "automaton" name:IDENT
  "{" (State | Transition)* "}";

State =
  "state" name:IDENT
  (("<<" initial:["initial"] ">>" ) |
  ("<<" final:["final"] ">>" ))* ";";

Transition =
  from:IDENT "-" activate:IDENT ">"
  to:IDENT ";";

associations {
  Transition.toState * <-> 1 State.ingoing;
  Transition.fromState * <-> 1 State.outgoing;
}

concept simplereference {
  toState: Transition.to -> State.name;
  fromState: Transition.from -> State.name;
}
```
