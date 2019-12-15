# Cross-Language Code Analysis and Refactoring

https://www.pst.ifi.lmu.de/Personen/team/mayer/papers/2012_09_23_SCAM2012_CrossLanguage.pdf


```
transformation RubyOnRails ( ruby: Ruby, rdb: RDB ) {
    top relation ClassToTable {
        classname: String;
        error domain ruby c:Class { superClass = sc:Class { name=’ActiveRecord’ }, name= classname}
        error domain domain rdb t:Table { name=pluralize(classname) }
    }
    top relation BelongsToToTable {
        entityname: String;
        error domain ruby m:MethodInvocation { name=’belongs to’, parent=c:Class {},
            parameter= p:Parameter { value= entityname }}
        error domain rdb col:Column { name= entityname + ’ id’, table= t:Table{}}
        when { ClassToTable(c, t) }
    }
    top relation HasOneManyToTable {
        entityname, classname: String;
        error domain ruby m:MethodInvocation { parent= c:Class { name=underline2camel(classname)},
            ((name = ’has one’, parameter= p:Parameter { value= singularize(entityname) }) |
            (name = ’has many’, parameter= p:Parameter { value= entityname }) }
        error domain rdb col:Column { name= classname + ’ id’, table= t2:Table{ name= entityname }}
        when { ClassToTable(c, t) }
    }
}

transformation Android2XML ( djava: DJava, xml: XML ) {
    top relation ActivityToLayout {
        layoutName : String;
        error domain djava a:Activity { referencedLayout=layoutName }
        warn domain xml f:XMLFile { parent = d:Directory { name=’layout’, parent= dd:Directory { name= ’res’ }},
            name = layoutName + ’.xml’ }
    }

    top relation IDReferenceDeclaration {
        reference: String;
        error domain djava lr:LayoutReference { activity= a, referencedID=reference }
        nocheck domain xml attr:Attribute { name=’android:id’, value= ’@+id/’ + reference },
            parent= e:Element { file= f }}
        when { ActivityToLayout(a, f) }
    }
}
```
