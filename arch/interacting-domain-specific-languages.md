# Developing Interacting Domain Specific Languages

refs: [https://www.open.ou.nl/bhr/mak07thesis.pdf](https://www.open.ou.nl/bhr/mak07thesis.pdf)

with Model Driven Architecture

 - Platform Independent Model The actual high-level description of an application (UML).
 - Platform Definition Model A model of a specific architecture (e.g. CORBA, .Net or a web-environment).
 - Platform Specific Model Executable description of an architecture instance.

## Domain Model Blog

```
domainmodel blog
concept User {
    name :: String (unique, name)
    email :: Email
    blogEntries <> [BlogEntry]
}
concept BlogEntry {
    title :: String (name, required)
    abstract :: Text
    contents :: Text (required)
    date :: Date (name)
    tags -> [Tag]
    replies <> [Reply]
    category :: {"Technical" : TECH, "Other" : NONTECH}
}
concept Tag {
    tagName :: String
}
```

## WebLayer DSL

```
weblayer blog
using domainmodel blog

page ViewBlog(BlogEntry be, User u){
    var Reply r

    header(be.title + " (written on " + be.date + ")")

    text(be.contents)

    table Tag t in be.tags { "Assigned tags" -> t.tagName }

    "Reply to this post:"
    form( input(r)
        action("Add reply", r.user = u; be.replies.add(r); be.save())
    )
    text("Replies for post " + be.title + " :")

    for Reply r in be.replies { show(r) }

    navigate("Home", Blog(u))
}
```

##  Interaction between DSLs

```
DomainModel(
    "blog"
    , [ ("Text", "String", [( "validate", "org.blog.domainmodel.validation.Text.validate" )])
    , ("Email", "String", [( "validate", "org.blog.domainmodel.validation.Email.validate" )])
    ]
    , [ Concept(
        "User"
        , "org.blog.domainmodel.User"
        , [ ConceptMember( Native(), "name", NativeType("String"), "java.lang.String", ["unique"] )
        , ConceptMember( Native(), "email", ExtendedType("Email"), "java.lang.String", [] )
        , ConceptMember( Composite(), "blogEntries", ListType(ConceptType("BlogEntry"))
        , "List<org.blog.domainmodel.BlogEntry>", [] )
        ]
        )
        , Concept(
        "BlogEntry"
        , "org.blog.domainmodel.BlogEntry"
        , [ ConceptMember( Native(), "title", NativeType("String"), "java.lang.String", ["required"] )
        <...>
        , ConceptMember( Native(), "category", EnumType(["TECH", "NONTECH"]), "org.blog.domainmodel.BlogEntry.Enum_category", [] )
        ]
        )
        , Concept(
        "Tag"
        , "org.blog.domainmodel.Tag"
        , [ConceptMember( Native(), "tagName", NativeType("String"), "java.lang.String", [] )
        ]
        )
        , Concept(
        "Reply"
        , "org.blog.domainmodel.Reply"
        [ <...> ]
        )
    ]
)
```

## BusinessRules DSL

```
page EditCustomer(Customer c, Account a) {
form(
    show(a)
    input(c)
    action("Add account",
        a.customer = c
        ; verifyAccount(a)
        ; c.number = createAccountNr(a)
        ; a.save()
    )
    )
}
```

```
BusinessRules(
    "banking"
    , [ Rule( "verifyAccount", [Concept("Account")], Void()
        , [ HaltOnFailure() ]
        , [ ("method", "com.corporate.verify")
        , ("webservice", "http://corporate.com/accounts/verificationWS")
    ]
    )
    , Rule( "createAccountNr", [Concept("Account")], Concept("AccountNumber")
        , []
        , [ ("method", "com.corporate.createNewAccount") ]
    )
] )
```
