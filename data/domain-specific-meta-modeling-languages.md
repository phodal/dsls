
[Domain-Specific Meta-Modelling Languages](http://miso.es/pubs/DSMM.pdf)

DSL Examples: 


```dsl
Syntax DSPM_MM for ProcessModel [”.pm_mm”] {
  model template Process Syntax@1 for ProcessModel
    ”process” ˆId ”{”
        (_:TaskTemplate |_:PerformerTemplate |_:SeqTemplate)∗
    ”}”;

 node template TaskTemplate@1 for Task
    (”final”)? ”task” ˆId #name
    with ”final” set final = true ;

 node template PerformerTemplate@1 for Performer
 ”performer” #name
    with name is id ;

 node template SeqTemplate@1 for Seq
    ”seq” ˆId ”:” #src ”−>” #tar
 with src redefinedBy from
    tar redefinedBy to;
 }
```

```Process
process SoftwareProcess {
    task Analysis ”requirements and analysis”
    task Design ”high and low level design”
    task Coding ”coding and unit testing”
    
    performer Analyst
    performer Designer
    performer Programmer
    
    seq a2d: Analysis −> Design
    seq d2c: Design −> Coding
}
```

```rule
@metamodel(name=ProcessModel,domain=source)
@metamodel(name=PMS,domain=target)
@model(potency=0)
rule Task2Ticket transform task : Source!Task to ticket : Target!Ticket
{
  ticket.name := task.ˆname;
  ticket.description := task.name;
  ticket.priority := task.duration−1;
 
  for (ref in task.references(”perfBy”))
     ticket.assignedTo ::= task.value(ref);
}

@lazy
rule Performer2User transform per : Source!Performer to usr : Target!User
{
 usr.name := per.name;
}
```

```operation
operation Analysis createOutps() {
  var req := new RequirementsDoc();
  self.output := req;
}

operation Design createOutps() {
  var dd := new DesignDoc();
  self.output := dd;
}
```

