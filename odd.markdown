---
layout: page
title: ODD Customization Assignment
permalink: /odd/
---


Documentation is a critical component of a digital project. 
In addition to teaching text encoding skills, this course aims to 
train students in a variety of documentation skills necessary 
to complete a quality digital project. The ODD customization 
assignment is one of two ways that you will document 
your TEI encoding. Your TEI files must validate your encoding 
against a customized schema you have created.

#### To Submit for Evaluation
- You will produce an ODD file customizing the TEI guidelines 
for the use of your particular project. It should include 
the following: 
	- Restriction of the elements used in your project.
	- Restriction of the attributes allowed on one or more elements.
	- A closed list of attribute values on one or more attributes.
	- Validating some aspect of your document against an external 
	list of values.
	- One or more embedded schematron rules.
	- For each element you customize (using the 
	[\<elementSpec\>](https://www.tei-c.org/release/doc/tei-p5-doc/en/html/ref-elementSpec.html){:target="_blank"} 
	element), you must include an 
	[\<exemplum\> ](https://www.tei-c.org/release/doc/tei-p5-doc/en/html/ref-exemplum.html){:target="_blank"}
	element with a snippet of code exemplifying 
	the customization and a 
	[\<remarks\> ](https://www.tei-c.org/release/doc/tei-p5-doc/en/html/ref-remarks.html){:target="_blank"}
	element offer a prose description 
	of how your project is using that element.
- You will use Roma, <https://romabeta.tei-c.org/settings>{:target="_blank"}, 
to transform your ODD file into a RelaxNG file and associate your TEI files with 
that customized schema. 

#### Due Date
- You should work locally on your own machine but commit and push (preferably to a draft 
repository) in GitHub on a regular basis. You will need to share a link to these files on GitHub by the 
due date for final work [11 December](../schedule/#11-december).