---
layout: page
title: Schematron Resources
permalink: /schematron/
---

This page contains information and links on the basics of creating and 
embedding schematron rules into an ODD customization of the TEI. 
For an introductory tutorial on writing an ODD customization, see 
the page on [ODD Resources](../ODDresources/). 

### Table of Contents:
* [Introduction](#introduction)
	* [Grammar and Rules](#grammarRules)
	* [Getting Started](#gettingStarted)
	* [Namespaces](#namespaces)
* [Writing Schematron Rules](#writingRules)
	* [\<pattern\> and \<rule\> Elements](#pattern)
	* [Setting the Context](#context)
	* [\<report\> and \<assert\> Elements](#rules)
	* [The Difference between ODD and Schematron](#difference)
	* [Messages and Documentation](#messages)
	* [Errors and Warnings](#errors)
	* [XPath](#xpath)
* [Embedding Rules in Your ODD](#embedding)
* [More Advanced Uses of Schematron](#advanced)
	* [The \<let\> Element](#let)
	* [Creating a List of Values](#list)
	* [Using Variables in Messages](#valueOf)
	* [Linking to Your Standoff](#linking)
	
_____
_____
## <a name="introduction"/>Introduction

### <a name="grammarRules"/>Grammar and Rules
A TEI ODD generates a RelaxNG file that articulates a 
customized grammar for the TEI document it is used to 
constrain. This 'grammar-based' approach produces a generalized 
set of constraints that govern how elements and the attributes 
they take function at the level of the document. Beyond such 
general constraints, most projects need a more 'rule-based' 
approach that can accommodate constraints that only apply 
in specific contexts. Schematron allows us to write rules that function 
only in specific contexts. You can associate your TEI file(s)
with both a RelaxNG file and with a separate Schematron file. 
Here we will write and test our Schematron rules in a Schematron 
file but then embed them in our ODD before generating our 
RelaxNG.


### <a name="gettingStarted"/>Getting Started
It is easy to make mistakes writing Schematron rules directly in 
an ODD file. It is best to write and test your rules in a 
Schematron file and then cut-and-paste the rule into your ODD. 
You want to start by simply opening a new file in oXygen and selecting 
a Schematron file. When you do, you should see the following (with some 
additional material inside the root element):
```
<?xml version="1.0" encoding="UTF-8"?>
<sch:schema xmlns:sch="http://purl.oclc.org/dsdl/schematron" queryBinding="xslt2"
    xmlns:sqf="http://www.schematron-quickfix.com/validator/process">
        
        ...
        
</sch:schema>
```

### <a name="namespaces"/>Namespaces
Schematron rules can be embedded in a TEI ODD file but we have to be 
careful about namespaces. The first thing we want to do is modify the 
default namespace declarations in our Schematron file. Adding 
```<sch:ns uri="http://www.tei-c.org/ns/1.0" prefix="tei"\>``` as the 
first child element of the root will help us avoid namespace errors. 
It will require that the rules we write have the "sch:" namespace before 
Schematron elements and the "tei:" namespace before the TEI elements 
we use is writing our rules. See the discussions below for examples of 
namespaces in practice.

```
<?xml version="1.0" encoding="UTF-8"?>
<sch:schema xmlns:sch="http://purl.oclc.org/dsdl/schematron" queryBinding="xslt2"
    xmlns:sqf="http://www.schematron-quickfix.com/validator/process">
    <sch:ns uri="http://www.tei-c.org/ns/1.0" prefix="tei"\>
        ...
        
</sch:schema>
```

_____
_____
## <a name="writingRules"/>Writing Schematron Rules


### <a name="pattern"/>\<pattern\> and \<rule\> Elements
Rules appear in a \<rule\> element that appear in a \<pattern\> element.
```
<sch:pattern>
    <sch:rule context="">
    
    </sch:rule>
</sch:pattern>
``` 

### <a name="context"/>Setting the Context
The value that Schematron brings to our ODD is the ability to write 
context-specific rules. We can write general customizations for how 
a \<p\> element can function in the whole TEI document using pure ODD. 
However, if some of the behaviors of the \<p\> element need to differ based on 
whether, for example, it appears in the header or the body of the document then we 
use Schematron. 

```
<sch:pattern>
    <sch:rule context="tei:teiHeader//tei:p">
        
    </sch:rule>
</sch:pattern>
```

Any rule that we write inside of this \<sch:rule\> element will apply 
only when the \<p\> element appears inside \<teiHeader\>. 

Note as well the use of namespaces on all elements, both Schematron and 
TEI!


### <a name="rules"/>\<report\> and \<assert\> Elements 
The two main child elements of \<rule\> are \<report\> and \<assert\>. 
It can be a bit tricky to keep these ideas separate in your mind. The 
following should help: 
* \<report\> = "send a message if the following is true"
	* The rule you write will be for something that should be excluded from 
	your data.
* \<assert\> = "send a message if the following is not true"
	* The rule you write should be for something that has to be true in 
	your data.
	
The following rule sends up an error message if a \<p\> element inside 
\<teiHeader\> contains a \<note\> element. A \<note\> element inside a \<p\> 
element would still be allowed in the \<body\> but it cannot appear in the 
header.  
```
<sch:rule context="tei:teiHeader//tei:p">
    <sch:report test="tei:note">
        ...
    </sch:report>
</sch:rule>
```	

In this example, the rule indicates that the only acceptable values 
for the @resp attribute on a \<note\> element inside the \<body\> are 
"#dls", "#ewb", and "#medComp". Note that the "." in the @test attribute 
indicates the current context, in this case the @resp attribute 
designated above.

```
<sch:rule context="tei:body/tei:note/@resp">
    <sch:assert test=". = '#dls' or . = '#ewb' or . = '#medComp'">
        ...
    </sch:assert>
</sch:rule>
```

### <a name="difference"/>The Difference between ODD and Schematron
The following example helps make the difference between ODD and Schematron 
more clear. Here we see the pure ODD customization indicating that 
in the TEI document associated with this schema only the four listed elements 
may appear as children of \<p\>. This means that in the TEI for this project, these 
four elements may in one place or another appear as children of \<p\>. 
In some contexts, however, all four are not allowed so Schematron can be used to 
set that context-specific rule.
```
<elementSpec ident="p" module="core" mode="change">
    <content>
        <sequence preserveOrder="false">
            <elementRef key="note"/>
            <elementRef key="pb"/>
            <elementRef key="persName"/>
            <elementRef key="placeName"/>
        </sequence>
    </content>
</elementSpec>
```
```
<sch:rule context="tei:teiHeader//tei:p">
    <sch:report test="tei:note">
        ...
    </sch:report>
</sch:rule>
```


### <a name="messages"/>Messages and Documentation
The writer of the Schematron rule must write the message that 
encoders get when they violate the rule. They can violate a rule 
by encoding something forbidden by a \<report\> rule or by encoding something 
that fails to meet the requirements of an \<assert\> rule. The 
message is the text node of the \<report\> or \<assert\>
element. It should make clear to the encoder what the error is. These 
messages also serve to document your project as they state 
what cannot be encoded in particular contexts and what must be 
encoded in particular contexts.
```
<sch:rule context="tei:teiHeader//tei:p">
    <sch:report test="tei:note">
        A &lt;p&gt; element in the &lt;teiHeader&gt; may not 
        contain a &lt;note&gt; element.
    </sch:report>
</sch:rule>
```

Note that ```&lt;``` and ```&gt;``` are required instead of "\<" and "\>" because 
these symbols would be understood as code not allowed in this text node.
When the encoder sees an error message in oXygen these will be rendered 
as the expected bracket symbols.

### <a name="errors"/>Errors and Warnings 
Not every violation of a rule has to result in an error. For example, if your project 
prefers certain values for an attribute but does not require only those values 
you can indicate this with a @role attribute.

```
<sch:rule context="tei:body//tei:note/@resp" role="warning">
    <sch:assert test=". = '#dls' or . = '#ewb' or . = '#medComp'">
        Preferred values: #dls, #ewb, #medComp
    </sch:assert>
</sch:rule>
```

If encoders use a value other than one of these three, they would see 
a yellow warning message appear in oXygen rather than a red error message. 
This can help encoders use the preferred options and if they persist
in their decision to deviate from the preference, the yellow warning 
message assists with the editing process.

### <a name="xpath"/>XPath
The XPath examples in the code snippets above are all pretty straightforward. 
As you use Schematron you should remember that all of the XPath 
axes and functions can be used to set the context of your rules. As your 
customization develops you will want to think about how you can use these 
features of XPath to set contexts that will strengthen the ability of your 
rules to constrain the encoding where you need to do so.

_____ 
_____ 


## <a name="embedding"/>Embedding Rules in Your ODD

Since the the rules in our Schematron file contain namespaces on all 
elements, we can simply cut-and-paste into our ODD. Schematron rules 
go inside a \<constraint\> element inside a \<constraintSpec\> element. 
The @ident attribute provides a name for the rule in the resulting 
RelaxNG. The @scheme attribute requires the value "schematron" since 
we are writing Schematron rules.
```
<elementSpec ident="p" module="core" mode="change">
    <content>
        <sequence preserveOrder="false">
            <elementRef key="note"/>
            <elementRef key="pb"/>
            <elementRef key="persName"/>
            <elementRef key="placeName"/>
        </sequence>
    </content>
    <constraintSpec ident="pInHeader" scheme="schematron">
        <constraint>
            <sch:rule context="tei:teiHeader//p">
                <sch:report test="tei:note">
                    A &lt;p&gt; element in the &lt;teiHeader&gt; may not contain a &lt;note&gt; element.
                </sch:report>
            </sch:rule>
        </constraint>
    </constraintSpec>
</elementSpec>
```
Recall that in this example the \<content\> element articulates all the possible 
elements that \<p\> can contain in the whole document and our 
Schematron rule addresses only the use of \<p\> in the header in 
order to constrain that element differently in that context.

_____ 
_____ 


## <a name="advanced"/>More Advanced Uses of Schematron
There is a great deal more you can do with Schematron than 
what we will discuss here. However, the following uses go a bit 
beyond the very basics and show some powerful ways to validate 
your TEI using Schematron.

### <a name="let"/>The \<let\> Element
The \<let\> element allows you to declare a variable. It takes 
a @name attribute which you will use when you want to call 
the variable later. It also takes a @value attribute with 
the path that points to the variable you want to declare.
```
<sch:rule context="tei:body//tei:note/@resp">
    <sch:let name="editorIDs" value="//tei:teiHeader//tei:editor/@xml:id"/>
    
</sch:rule>
```
The \<let\> element goes inside the \<rule\> element. As we have come to expect 
the path in the @context attribute sets the context in which the rule will fire. 
The path in the @value attribute points to one or more values on the 
@xml:id attribute on any \<editor\> element in the header. 

### <a name="list"/>Creating a List of Values
A path often leads to multiple values. If you want to use those values 
to validate something else in your document, you will need to know several 
functions. In the example below, the result of the first \<let\> is a list 
of @xml:id attribute values. An @xml:id has a datatype of 
"[ID](https://www.tei-c.org/release/doc/tei-p5-doc/en/html/ref-att.global.html#tei_att.xml-id){:target="_blank"}". 
The value of the @resp attribute in our TEI that we are trying to constrain with this rule requires a datatype of 
"[teidata.pointer](https://www.tei-c.org/release/doc/tei-p5-doc/en/html/ref-att.global.responsibility.html#tei_att.resp){:target="_blank"}" 
and needs a "#" before the value. Variables and XPath functions come together 
to allow us to constrain our TEI.

```
<sch:rule context="tei:body//tei:note/@resp">
    <sch:let name="editorIDs" value="//tei:teiHeader//tei:editor/@xml:id"/>
    <sch:let name="IDValues" value="for $i in $editorIDs return concat('#', $i)"/>
    ...
</sch:rule>
```

A ```$``` calls a variable. So ```$editorsIDs``` in the second \<let\> element calls 
the variable extablished in the first \<let\> element. The second \<let\> 
element builds pointer values for the @resp attribute out of @xml:id 
values on the \<editor\> element. Accomplishing this requires several steps in 
the @value attribute. This @value attribute says, 'for each instance [```$i```] of 
a variable in the list ```$editorIDs```, return for this variable a concatenation 
of the string "#" and each instance [```$i```] of the variable.' Note that this will 
work whether there is one value returned by ```$editorIDs``` or many.

```
<sch:rule context="tei:body//tei:note/@resp">
    <sch:let name="editorIDs" value="//tei:teiHeader//tei:editor/@xml:id"/>
    <sch:let name="IDValues" value="for $i in $editorIDs return concat('#', $i)"/>
    <sch:assert test=". = $IDValues">
        ...
    </sch:assert>
</sch:rule>
```

Calling the variable ```$IDValues``` in the @test of the \<assert\> will require 
the @resp attribute on \<note\> in the \<body\> to contain a properly-formatted pointer 
to one of the editors in the header. 

Compare this code snippet to the example we used above for \<assert\>:
```
<sch:rule context="tei:body//tei:note/@resp">
    <sch:assert test=". = '#dls' or . = '#ewb' or . = '#medComp'">
        Preferred values: #dls, #ewb, #medComp
    </sch:assert>
</sch:rule>
```

Writing this Schematron rule required looking at the header and finding 
all of the values of @xml:id on \<editor\> elements. This works, but 
using variables means that if an editor is added to the header, the 
Schematron rule has to be manually changed. Using variables means that 
Schematron does that work for us. On a large project this can save time 
and prevent errors.

### <a name="valueOf"/>Using Variables in Messages
Not only do variables help to keep validation up to date as a project 
evolves, they can also help communicate with encoders as they work. 
Using a \<value-of\> element with a @select attribute that calls a 
variable displays the acceptable values in an error message.
```
<sch:rule context="tei:body//tei:note/@resp">
    <sch:let name="editorIDs" value="//tei:teiHeader//tei:editor/@xml:id"/>
    <sch:let name="IDValues" value="for $i in $editorIDs return concat('#', $i)"/>
    <sch:assert test=". = $IDValues">
        Acceptable values: <sch:value-of select="string-join($IDValues, ', ')"/>.
    </sch:assert>
</sch:rule>
```
"String-join" is another XPath function that is used in this example to 
create a comma-separated list out of the list of variables called as the 
first argument of the function.

### <a name="linking"/>Linking to Your Standoff
Standoff TEI markup is used to organize information about things 
appearing in your encoding. Lists of persons and places with name(s), dates, etc. 
relevant to those entities can be collected there. Each entity is identified 
with an @xml:id. In the example above, we saw how a variable can be used to create 
pointers to a list of identifiers within a TEI document. Variables can also 
be used in the same way when the identifiers appear in a differnt document.

```
<sch:rule context="tei:body//tei:persName/@ref">
    <sch:let name="indexDoc" value="doc('https://raw.githubusercontent.com/dlschwartz/sandbox/master/SeverusAntiochLettersIndex.xml')"/>
    <sch:let name="personIDs" value="$indexDoc//tei:listPerson/tei:person/@xml:id"/>
    <sch:let name="personRefValues" value="for $i in $personIDs return concat('#', $i)"/>
    <sch:assert test=". = $personRefValues">
        Acceptable values: <sch:value-of select="string-join($personRefValues, ', ')"/>
    </sch:assert>
</sch:rule>
```
In this example, the first \<let\> points to a file on GitHub (note that you have 
to point to the "raw" file). The second \<let\> builds a list of @xml:id values 
and the third \<let\> builds a list of values that point to the entities identified 
with those @xml:id attributes. 

_____
_____ 


#### Acknowledgements
This page draws upon the following sources:
* Elisa Beshero-Bondar and David Birnbaum, "XPath for processing XML and managing 
projects,"  <https://ebeshero.github.io/UpTransformation/index.html>{:target="_blank"}.
* Elisa Beshero-Bondar, "Guide to Schema Writing with Schematron," <https://newtfire.org/courses/dh/explainSchematron.html>{:target="_blank"}.
* Wendell Piez and Debbie Lapeyre, "Introduction to Schematron," <http://www.mulberrytech.com/papers/schematron-Philly.pdf>{:target="_blank"}.
