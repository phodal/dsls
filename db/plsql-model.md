# PL/SQL Model Extract

```
rule createPLSQLDefinition
    from create_package cp
    to PLSQLDefinition
    queries
        seqt : /cp//#seq_of_statements;
    mappings
        triggers = seqt;
end_rule

rule createTriggerBlock
    from seq_of_statements seqt
    to TriggerBlock
    queries
        stats : /seqt/#statement;
    mappings
        statements = stats;
end_rule

rule createReturnStatement
    from statement/return_statement st
    to ReturnStatement
    mappings
end_rule

rule createFunctionCallStatement
    from statement/function_call st
    to FunctionCallStatement
    queries
        fc : /statement/#function_call;
        iden : /fc/user_defined_function//#identifier;
        params : /fc///#call_parameter;
    mappings
        name = iden.ID;
    parameters = params;
end_rule

rule createFunctionCallParamForFunctionCall
from call_parameter cp
to FunctionCallParameter
queries
iden : /cp/parameter_name/#identifier;
mappings
name = iden.ID;
end_rule
```
